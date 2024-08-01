With the Kiota onboarding, our code will be using the following four solutions:
- Kiota generator
- Kiota core libraries
- Graph core library which will be dependending on the Kiota core.
- Graph service library containing Kiota generated request builders.

The proposed plan to set the GitHub repositories for the above solutions is as follows : 

- Kiota (the generator) - common for all languages
	
- kiota-<language>
	
- msgraph-sdk-<language>-core
	
- msgraph-sdk-<language>
	
- msgraph-beta-sdk-<language>
	
- (optional) msgraph-china-sdk-<language>
	
- (optional) msgraph-beta-china-sdk-<language>
 
Concerns to be discussed more:
  
1. Duplicate names - Consider adding new repositories for all the Kiota generated libraries and discontinuing the repos that use the current code generation process.
  In this case, we will need to think of duplicate repository names, that is, the old repo and the new repo cannot be named as `msgraph-sdk-<language>`.
2. JavaScript - The current core library is named as `msgraph-sdk-javascript`. 

  
