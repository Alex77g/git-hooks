# sitegeist/git-hooks [![Build Status](https://travis-ci.org/sitegeist/git-hooks.svg)](https://travis-ci.org/sitegeist/git-hooks)

> Git-Hooks which are supporting our workflow and QA process.

## Installation
``` bash
git clone https://github.com/sitegeist/git-hooks.git $HOME/.sitegeist-hooks && cd $HOME/.sitegeist-hooks && ./install && cd
```

## CLI
`cd` into your target directory which contains your local `.git/` repository and run hook.

``` bash
hook install
```
Afterwards all available hooks should be installed in your local repository.

#### Commands
| Command            | Description                                                     |
| ------------------ | --------------------------------------------------------------- |
| `hook`             | Prints the usage guidelines.                                    |
| `hook install`     | Installs all hooks in your current working directory.           |
| `hook self-update` | Updates all installed hooks which you've installed via the CLI. |
| `hook help`        | Prints the usage guidelines.                                    |
| `hook help:hooks`  | Prints a list of all installed hooks.                           |

#### Why bother install them globally?
These git-hooks are conventional hooks, not project specific ones. Installing them globally reduces the amount of time you need to invest once a new feature has been released or a bug has been found in one of the hooks.

In that case, just run `self-update` once and all hooks which you've installed via the CLI are updated. The CLI and the corresponding hooks will also update itself automatically every 30 days on every CLI run. This prevents the hooks from being in a obsolete state.

## Available git-hooks
#### pre-commit
Lints all changed `.js` files via [xo](https://github.com/sindresorhus/xo) if the directory contains a local `xo` binary.
If the lint process exits with an error code, the commit will be aborted, until you fix the errors.

The pre-commit hook also checks if a `package.json` file is in your changeset, and will automatically create and commit a `npm-shrinkwrap.json` file for you. *Note:* This functionality is kind of useless if you are using `> npm@3.0.0` - [Related issue](https://github.com/npm/npm/issues/5083).

#### post-merge
If the `package.json` or `composer.json` has been changed in the upstream,
this hook will automatically run either `npm prune && npm update` or `composer install` so your local dependencies match the current checked out HEAD.

#### prepare-commit-msg
Evaluates the commit message against the [TYPO3 Commit guidelines](#guidelines).

In case your current branch is a feature branch, the issue number of the branch gets parsed
and automatically appended to the commit message.

For example, if your current branch is called `task/29381/taskDescription`, and you commit
``` bash
git commit -m "[TASK] Add person select to the quick booking widget"
```

The final commit message in your history will be
```
[TASK] Add person select to the quick booking widget

refs #29381
```

## Extending the available hooks.
You can extend the existing hooks by creating a `.hook.yml` in your git repositories root directory.
From there on, all paths to hook extensions are relative to this file.

The hook extensions itself need to be executable, you can achieve this with `chmod +x path/to/hook/extension`.
Each hook is immediately executed after the global hook.

We expect the extension to exit with code 0.
Also, the arguments from git which where passed to our hooks are propagated to your hook extension,
so your hook extension acts like a standalone hook which is traditionally placed in the `.git/hooks/` directory.

An example `.hook.yml`.
```yaml
extend:
  pre_commit: Build/hooks/pre-commit
  post_merge: Build/hooks/post-merge
  prepare_commit_msg: Build/hooks/prepare-commit-msg
```


## <a name="guidelines"></a> Commit message guidelines
In short, a commit message must be prefixed with either `[FEATURE]`, `[TASK]`, `[BUGFIX]`, `[DOCS]` or `[CLEANUP]`. F.e:
``` bash
git commit -m "[FEATURE] Add person select to the quick booking widget"
```

Additionally, for breaking changes, you should specify the prefix `[!!!]`, and for not finished work, the `[WIP]` prefix.
Note that both of them should be immediately followed by one of the prefixes above. F.e.:
``` bash
git commit -m "[!!!][TASK] Change the person argument of the booking API for consistency"
```

For an overview of the commit message guidelines, please visit the official [TYPO3 commit guidelines page](https://wiki.typo3.org/CommitMessage_Format_(Git)#Commit_Message_rules_for_TYPO3_CMS).

## License
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
