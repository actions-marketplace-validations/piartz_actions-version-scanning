# AVS: Actions Version Scanner

This tool assists GitHub workflow mantainers into enumeration and update of GitHub Actions versioning. Referencing GitHub Actions versions by release tag or commit is considered a better practice than using branch or tag names: it allows consistency in your supply chain and avoids uncontrolled changes from external repositories. 

## Usage

`scanner [-h] [-p]`

`-h`: Opens the help menu.
`-p`: Defines the path for a directory or file to be scanned (default: ./scan/)

## Logic explanation

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