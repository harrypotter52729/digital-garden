
- API login -> save state -> inject

1. Create a authentication script.
	1. use plyawright request fixture
	2. hit backend login api directly
	3. save token/cookies into json auth file (add to .gitignore to this files)
2. Configure playwright to run setup first (instead of UI)
3. so when tests run automatically login 

For multiple parallel tests when logged in to same user it cases database conflicts
- user custom Test Fixture

## Multi account architechture

- Use playwright custom fixture with options  
  
1. During setup authenticate all accounts.
2. store them in seperate files
3. use custom fixture to route correct state to the test.
	1. in custom fixture override the default page object.
	2. use custom option (like accrole/type) to decide which JSON file to insert.
	3. in test file imprt test from custom fixture instead of @playwright/test. to pick default accounts.  
	4. override fixture usring test.use() to pick the account type