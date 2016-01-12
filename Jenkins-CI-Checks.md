We have Continuous Integration (CI) tests set up using Jenkins. Each Pull Request will be automatically built and tested in all of the configurations the code is expected to support, using the Jenkins CI system. Pull Requests must pass all CI checks before they will be merged.

We also test each commit which is merged into master to ensure no breaks occur when merging. Additionally, we test some configurations which are more expensive to test on a daily basis to ensure we cover those scenarios as well, even if we can't cover them for every contribution.

Specifically, we test the following:

1. Do all configurations build and pass unit tests?
2. Do the files in the change have the required line endings?
3. Do the files in the change feature the required Microsoft copyright header?
4. Has the change been reviewed and signed off by a project maintainer?

# Re-Running Tests

Occasionally, a test will fail due to an intermittent issue in the Jenkins system, or for other intermittent reasons. When this occurs, you can request __@dotnet-bot__ to re-run a specific test or all of your tests.

To rerun a test, copy the name of the test you want to re-run from the GitHub page or above and write a comment using the following template:

```
@dotnet-bot test [test_name] please
```

For example:

```
@dotnet-bot test Windows x64_release please
```
	
If you would like to re-run all of the Jenkins tests:

```
@dotnet-bot test this please
```

# Making changes to the Jenkins CI configuration.

If you want to change or add build configurations into Jenkins CI, you will need to update the [netci.groovy](https://github.com/Microsoft/ChakraCore/blob/master/netci.groovy) file, and create a Pull Request.

After you update the file you will need to create a pull request and verify the configuration change did what you expected. You should ask __@dotnet-bot__ to test the change to the CI, like so:

```
@dotnet-bot test ci please
```

If there are errors in your script, this check will fail. Once it succeeds, you can then view the configurations via the Details link that appears next to the resulting configuration item, and ensure that your changes to the configuration script did what you intended.

Unfortunately there is no way to see whether the code will pass the new configurations before merging the change to the CI. For that, we will have to merge your changes and see what happens on the subsequent builds. If failures occur, we may revert your change and reopen your Pull Request and ask you to correct the issues. If tests are being run for the first time they may fail. The responsibility for fixing the test failures may fall on you or the original developers depending on the nature of the issue.

Pull Requests and Issues regarding the Jenkins CI configuration will be tagged as [Continuous Integration](https://github.com/Microsoft/ChakraCore/labels/Continuous%20Integration) so that we know those issues require special attention.

# FAQ

## Why did the EOL Check fail?

Most likely you're developing on Windows and the Git settings you're using are incorrect, causing you to accidentally commit Windows-style line endings in your changes, when we prefer to have Git normalize the line endings in most files (unless otherwise indicated in the `.gitattributes` file). Try running this command in the core directory:

```sh
git config --get core.autocrlf
```

You should see the value 'true' printed out. If you do not see the value 'true' printed out, you will need to follow these steps:

```sh
git config core.autocrlf true

# Check out a new branch which doesn't include the changes on the problematic branch (oldbranch)
git checkout master
git checkout -b <newbranch>
git merge --squash <oldbranch>

# Add and confirm your changes.  
git add .
git status
git diff --cached
git commit -m "<message>"

# Force-push your branch to update your Pull Request.
```