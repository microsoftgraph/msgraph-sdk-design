# Raptor Pipeline as a Deployment Gate

## Objectives

Provide an Azure pipeline (or a set of Azure pipeline jobs), which takes arbitrary staging metadata and accompanying documentation input and outputs a decision on whether metadata can be signed off for release based on generated SDK and snippet quality.

## Requirements

- Staging metadata should be an input variable
  - Metadata should be available as a public URL or as an Azure DevOps artifact
- Docs branch should be an input variable
  - If the metadata is accompanied with new documentation, it should be checked into a branch of `microsoft-graph-docs` repo and branch name should be provided to the pipeline
- Snippet Generation should be available as a tool to ApiDoctor

## Current State

The tools that are required to answer the question in the [Objectives](#Objectives) section are spread across the following repos.
1. [microsoftgraph/MSGraph-SDK-Code-Generator](https://github.com/microsoftgraph/MSGraph-SDK-Code-Generator)
    - Tool to generate C# SDK (Typewriter)
    - Has a binary release
    - Can be compiled as a .NET Framework executable
2. [microsoftgraph/msgraph-sdk-raptor](https://github.com/microsoftgraph/msgraph-sdk-raptor)
    - Tool to verify whether generated code snippets compile
    - Creates compilation test cases for each V1 and Beta snippet live at docs.microsoft.com
    - Consumes Microsoft.Graph V1 and Beta DLLs as Nuget packages
3. [microsoftgraph/msgraph-sdk-dotnet](https://github.com/microsoftgraph/msgraph-sdk-dotnet)
    - SDK repo to compile generated code
4. [microsoftgraph/microsoft-graph-explorer-api](https://github.com/microsoftgraph/microsoft-graph-explorer-api)
    - Provides an HTTP endpoint to generate C# snippets from HTTP snippets. The HTTP endpoint is the only current option that is exposed for this functionality.
5. [microsoftgraph/microsoft-graph-docs](https://github.com/microsoftgraph/microsoft-graph-docs)
    - Contains HTTP snippets
    - New documentation can be added by forking a branch
6. [OneDrive/apidoctor](https://github.com/OneDrive/apidoctor)
    - Tool to extract HTTP snippets from docs and write generated snippet references
    - Snippet generation functionality is not available in the main repo.
      - It is available at [andrueastman/apidoctor](https://github.com/andrueastman/apidoctor) / [generate-snippets](https://github.com/andrueastman/apidoctor/tree/generate-snippets).
      - The functionality is coupled with a PR generation to docs repo upon completion of snippet generation.
      - Hits public Graph Explorer API HTTP endpoint to generate the snippets

## Proposed Solution
### Input variables
1. `version` : `v1.0` or `beta`
2. either `metadataURL` or metadata artifact from another pipeline run
3. `docsBranch` : branch on `microsoft-graph-docs` repo, containing new documentation
4. `typewriterReleaseVersion` : true if the released version of typewriter to be used from Github releases. Otherwise it is expected to be compiled from source.

### Pipeline
  1. Prepare metadata input
     1. If metadata is given as a public URL in `metadataURL`, set `metadataPath` variable to that
        1. Typewriter can take URL as metadata input
     1. If metadata is an Azure DevOps artifact
        1. Download the artifact to a local path
        2. Save that local path as `metadataPath`
  2. Build snippet generation tool
     1. Checkout `microsoft-graph-explorer-api` repo
     2. Build snippet generation tool
     3. Save executable's path as `snippetGeneratorPath`
  3. Checkout `microsoft-graph-docs` repo
     1. Use `docsBranch` variable if defined
     2. Use `master` if not defined
     3. Save repo path as `docsRepoPath`
  4. Run apidoctor
     1. checkout `OneDrive/apidoctor` repo
     2. run `apidoctor` with the following inputs
        1. `docsRepoPath`
        2. `snippetGeneratorPath`
  5. SDK generation
     1. checkout `msgraph-sdk-dotnet` repo
        1. Save path as `dotnetRepoPath`
     2. If `typewriterReleaseVersion` is `true`
        1. Download `Typewriter` from Github releases
     3. Else
        1. Checkout `MSGraph-SDK-Code-Generator` repo
        2. Build `Typewriter`
        3. Run Typewriter Tests
     4. Run `Typewriter` for `CSharp`
        1. Consumes `metadataPath`
     5. Clean old generated files in `dotnetRepoPath`
     6. Copy new generated files into `dotnetRepoPath`
     7.  Build `Microsoft.Graph` solution
     8.  Run `Microsoft.Graph` tests
     9.  Save `Microsoft.Graph.dll` path as `dllPath`
  6. Raptor
     1. checkout `msgraph-sdk-raptor` repo
     2. Reference `Microsoft.Graph.dll` using `dllPath`
     3. Using `version` information attempt to compile corresponding snippets from `docsRepoPath`
     4. Give a thumbs up if all tests pass

### Prerequisites for defining the pipeline above
1. Decouple apidoctor from generating a PR after snippet generation
   1. Current logic is already running as part of a weekly pipeline. PR generation logic can be moved out of apidoctor.exe to an explicit pipeline step.
2. Have snippet generation logic as a class library or as an executable for apidoctor's consumption instead of HTTP calls
3. Have Raptor
   1. Either reference the project directly in `msgraph-sdk-dotnet` repo
   2. Or take `Microsoft.Graph.dll` location as an input

## Performance Considerations
- Is typewriter build time a consideration?
  - `apidoctor` analysis and extraction of HTTP snippets is the slowest step in all of the proposed steps above and we don't have an in-house solution to speed that up at the moment. This is the step that precedes the HTTP calls to Graph Explorer API for generating snippets. We can consider saving that state as a reusable input if we continuously try to build from `master` branch. Not sure about what the structure of that runtime state looks like.

## Open Issues
1. Need to sync with AGS team to see where this pipeline is going to be located.
2. Is this going to be a stand-alone pipeline or part of an existing pipeline? The design can easily fit both pictures and our preference would be a stand-alone pipeline for testing purposes on our end.