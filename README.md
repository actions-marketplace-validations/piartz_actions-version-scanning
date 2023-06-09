# AVS: Actions Version Scanner

This action assists GitHub workflow mantainers into enumeration and update of GitHub Actions versioning. Referencing GitHub Actions versions by release tag or commit is considered a better practice than using branch or tag names: it allows consistency in your supply chain and avoids uncontrolled changes from external repositories. This tool provides an output of which actions are referred by commit hash, and which ones don't. 

## Using the action

Reference the action as usual. Remember that referencing to a commit hash after the `@` is considered a best practice. 

```yaml
steps:
  # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
  - uses: actions/checkout@v3
  - uses: piartz/actions-version-scanning@v0.1
```

If no arguments are passed to the action, it will by default look inside your `.github/workflows` directory. To perform the scan in another directory or just on a specific file, you can pass it as follows:

Directory:
```yaml
- uses: piartz/actions-version-scanning@v0.1
  with:
    path: 'alternative-directory'
```

File:
```yaml
- uses: piartz/actions-version-scanning@v0.1
  with:
    path: 'alternative-directory/specific-file.yml'
```

## Using as a CLI tool

Since this is purely a shell script (bash), you can either clone this repository and execute it directly (`./avs`), or copying it to a folder on your path to make it available as a command (for example, `<sudo> cp avs /usr/bin/avs`). 

Execution permissions should be already granted, but if that is not the case, you can simply enable them by `chmod +x avs`. 

### Help menu as a CLI tool

`avs [-h] [-p]`

`-h`: Opens the help menu.
`-p`: Defines the path for a directory or file to be scanned (default: ./scan/)


## How does it work?

The scanner filters out lines starting with `uses: ` that comply with the format to call a GitHub action. It then checks the data after the `@` character that references a version for such action. 

- If the data is not exactly 40 characters, then it will be treated as the name for a branch or tag. 
- If the data is exactly 40 characters, and complies with the format of a commit hash (lowercase characters from a to f, or numbers), it will be detected as a commit hash. Otherwise, it will be treated as a name for a branch or tag. 

## Limitations and edge cases

- The script only detects names of branches or tags up to 255 characters, due to limitations of the `grep` command. 
- In the uncommon case where a branch name or tag randomly complies with the format of a commit hash, it will be falsely detected as such. Think about a branch name that is *exactly* 40 characters long, using only numbers and lowercase letters in the `a-f` range. Examples:
`aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa` or `1234567890123456789012345678901234567890`.

## Future work

- **Active search**: curl the detected lines against the GitHub API to verify from the server side if the name corresponds to a branch, tag or commit. This removes the edge case where a 40 character branch/tag reference that complies with the format of a hash might be detected as a commit. 
- **Automatic polling of new versions**: For every commit hash reference, check if there are new versions available, so maintainers can choose to update it without going to the repository of the action on a browser. 
