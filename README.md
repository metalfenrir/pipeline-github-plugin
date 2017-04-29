Table of Contents
=================
  * [Pipeline: GitHub](#pipeline-github)
  * [License](#license)
  * [Prerequisites](#prerequisites)
  * [Credentials](#credentials)
  * [Global Variables](#global-variables)
    * [pullRequest](#pullrequest)
  * [Auxiliary Classes](#auxiliary-classes)
    * [CommitStatus](#commitstatus)
    * [Commit](#commit)
    * [CommitFile](#commitfile)
    * [IssueComment](#issuecommet)
    * [ReviewComment](#reviewcomment)
  * [Examples](#examples)


# Pipeline: GitHub
The entry point for all of this plugin’s functionality is a `pullRequest` global variable, available to pipeline scripts when the plugin is enabled and the prerequisites are met. 

# License
MIT

# Prerequisites

Jenkins running Java 8 or higher.

For the additional global variables, provided by this plugin, to be available to pipeline builds, the projects/jobs must be automatically created by the GitHub Organization folder/project type. 

See: [GitHub Branch Source Plugin](https://go.cloudbees.com/docs/cloudbees-documentation/cje-user-guide/index.html#github-branch-source)

# Credentials

Currently all operations against GitHub will be performed using the builds `Checkout Credentials`, if no `Checkout Credentials` are configured then the `Scan Credentials` will be used instead.

However you can override this in a pipeline script by calling `setCredentials(String userName, String password)` before any properties or methods are accessed/invoked on the `pullRequest` global variable.

```groovy
pullRequest.setCredentials('John.Smith', 'qwerty4321')
```

If you plan to use this plugin to add/modify/remove comments, labels, commit statuses etc. Please ensure that the required permissions are assigned to the token supplied in the credentials (`Checkout`/`Scan`/`Manually`).

# Global Variables

## `pullRequest`

### Properties

Name | Type | Writable | Description
-----|------|----------|------------
id | `Integer` | false | 
state | `String` | **true** | Valid values `open` or `closed`
number | `Integer` | false
url | `String` | false
patch_url | `String` | false
diff_url | `String` | false
issue_url | `String` | false
title | `String` | **true**
body | `String` | **true**
locked | `Boolean` | **true** | Accepts `true`, `false` or `'true'`, `'false'`
milestone | `Integer` | **true**
head | `String` | false
base | `String` | **true** | Name of the base branch in the current repository this pull request targets
files | `Iterable<CommitFile>` | false
assignees | `List<String>` | **true** | Accepts a `List<String>`
commits | `Iterable<Commit>` | false
comments | `Iterable<IssueComment>` | false
review_comments | `Iterable<ReviewComment>` | false
labels | `Iterable<String>` | **true** | Accepts a `List<String>`
statuses | `List<CommitStatus>` | false 
updated_at | `Date` | false
created_at | `Date` | false
created_by | `String` | false
closed_at | `Date` | false
closed_by | `String` | false
merged_at | `Date` | false
merged_by | `String` | false
commit_count | `Integer` | false
comment_count | `Integer` | false
additions | `Integer` | false
deletions | `Integer` | false
changed_files | `Integer` | false
merged | `Boolean` | false
mergeable | `Boolean` | false
merge_commit_sha | `String` | false
maintainer_can_modify | `Boolean` | **true** | Accepts `true`, `false` or `'true'`, `'false'`  


### Methods

#### Merge

> String merge(__[String commitTitle, String commitMessage, String sha, String mergeMethod]__)

Returns the merge's SHA/commit id.

#### Commit Status

> CommitStatus createStatus(String status __[, String context, String description, String targetUrl]__)

#### Labels
> void addLabels(String...labels)

> void removeLabel(String label)

#### Assignees
> void addAssignees(List<String> assignees)

> void removeAssignees(List<String> assignees)

#### Review Comments
> ReviewComment reviewComment(String commitId, String path, int position, String body)

> ReviewComment editReviewComment(long commentId, String body)

> ReviewComment replyToReviewComment(long commentId, String body)

> void deleteReviewComment(long commentId)

#### Pull Request Comments (Issue Comments)
> IssueComment comment(String body)

> IssueComment editComment(long commentId, String body)

> void deleteComment(long commentId)

#### Misc
> void setCredentials(String userName, String password)

# Auxiliary Classes

## CommitStatus
### Properties
Name | Type | Writable | Description
-----|------|----------|------------
id | `String` | false | 
url | `String` | false
status | `String` | false | One of `pending`, `success`, `failure` or `error`
context | `String` | false
description | `String` | false
target_url | `String` | false
created_at | `Date` | false
updated_at | `Date` | false
creator | `String` | false

### Methods
None.

## Commit
### Properties
Name | Type | Writable | Description
-----|------|----------|------------
sha | `String` | false
url | `String` | false
author | `String` | false
committer | `String` | false
parents | `List<String>` | false | List of parent commit SHA's
message | `String` | false |
comment_count | `Integer` | false
comments | `Iterable<ReviewComment>` | false 
additions | `Integer` | false
deletions | `Integer` | false
total_changes | `Integer` | false
files | `List<CommitFile>` | false | List of files added, removed and or modified in this commit
statuses | `List<CommitStatus>` | false | List of statuses associated with this commit

### Methods
#### Commit Status
> CommitStatus createStatus(String status __[, String context, String description, String targetUrl]__)

#### Review Comment
> ReviewComment comment(String body __[, String path, Integer position]__)

## CommitFile
### Properties
Name | Type | Writable | Description
-----|------|----------|------------
sha | `String` | false
filename | `String` | false
status | `String` | false | One of 
patch | `String` | false
additions | `Integer` | false
deletions | `Integer` | false
changes | `Integer` | false
raw_url | `String` | false
blob_url | `String` | false

### Methods
None.

## IssueComment
### Properties
Name | Type | Writable | Description
-----|------|----------|------------
id | `Integer` | false
url | `String` | false
user | `String` | false
body | `String` | **true**
created_at | `Date` | false
updated_at | `Date` | false

### Methods
> void delete()

## ReviewComment
### Properties
Name | Type | Writable | Description
-----|------|----------|------------
id | `Integer` | false
url | `String` | false
user | `String` | false
created_at | `Date` | false
updated_at | `Date` | false
commit_id | `String` | false
original_commit_id | `Integer` | false
body | `String` | **true**
path | `String` | false
line | `Integer` | false
position | `Integer` | false
original_position | `Integer` | false
diff_hunk | `String` | false

### Methods
> void delete()


# Examples

## Pull Requests

### Updating a Pull Request's title and body
```groovy
pullRequest['title'] = 'Updated title'
pullRequest['body'] = pullRequest['body'] + '\nEdited by Pipeline'
```

### Closing a Pull Request
```groovy
pullRequest['status'] = 'closed'
```

### Creating a Commit Status against the head of the Pull Request
```groovy
pullRequest.createStatus(status: 'success',
                         context: 'continuous-integration/jenkins/pr-merge/tests',
                         description: 'All tests are passing',
                         targetUrl: "${JOB_URL}/testResults")
```

### Locking and unlocking a Pull Request's conversation
```groovy
if (pullRequest['locked']) {
    pullRequest['locked'] = false
}
```

### Merging a Pull Request
```groovy
if (pullRequest['mergeable']) {
    pullRequest.merge('merge commit message here')
}
// or
if (pullRequest['mergeable']) {
    pullRequest.merge(commitTile: 'Make it so..', commitMessage: 'TO BOLDLY GO WHERE NO MAN HAS GONE BEFORE...', mergeMethod: 'squash')
}
```

### Adding a label
```groovy
pullRequest.addLabel('Build Passing')
```

### Removing a label
```groovy
pullRequest.removeLabel('Build Passing')
```

### Replacing all labels
```groovy
pullRequest['labels'] = ['Bug', 'Feature']
```

### Adding an assignee
```groovy
pullRequest.addAssginee('Spock')
```

### Removing an assignee
```groovy
pullRequest.removeAssginee('McCoy')
```

### Replacing all assignees
```groovy
pullRequest['assignees'] = ['Data', 'Scotty']
```

### Listing all added/modified/removed files
```groovy
for (commitFile in pullRequest['files']) {
    echo "SHA: ${commitFile['sha']} File Name: ${commitFile['filename']} Status: ${commitFile['status']}"
}
```

### Adding a comment
```groovy
def comment = pullRequest.comment('This PR is highly illogical..')
```

### Editing a comment
```groovy
pullRequest.editComment(comment['id'], 'Live long and prosper.')
// or
comment['body'] = 'Live long and prosper.'
```

### Deleting a comment
```groovy
pullRequest.deleteComment(commentId)
// or
comment.delete()
```

### Adding a review comment
```groovy
def commitId = 'SHA of the commit containing the change/file you wish to review';
def path = 'src/main/java/Main.java'
def lineNumber = 5
def body = 'The review comment'
def comment = pullRequest.reviewComment(commitId, path, lineNumber, body)
```

### Editing a review comment
```groovy
pullRequest.editReviewComment(comment['id'], 'Live long and prosper.')
// or
comment['body'] = 'Live long and prosper.'
```

### Deleting a review comment
```groovy
pullRequest.deleteReviewComment(comment['id'])
// or
comment.delete()
```

### Replying to a review comment
```groovy
pullRequest.replyToReviewComment(comment['id'], 'Khaaannnn!')
// or
comment.createReply('Khaaannnn!')
```

### Listing a Pull Request's commits
```groovy
for (commit in pullRequest['commits']) {
   echo "SHA: ${commit['sha']}, Committer: ${commit['commiter']}, Commit Message: ${commit['message']}"
}
```

### Listing a Pull Request's comments
```groovy
for (comment in pullRequest['comments']) {
  echo "Author: ${comment['user']}, Comment: ${comment['body']}"    
}
```

### Listing a Pull Request's review comments
```groovy
for (reviewComment in pullRequest['review_comments']) {
  echo "File: ${reviewComment['path']}, Line: ${reviewComment['line']}, Author: ${reviewComment['user']}, Comment: ${reviewComment['body']}"    
} 
```

### Listing a commit's statuses
```groovy
for (commit in pullRequest['commits']) {
  for (status  in commit['statuses']) {
     echo "Commit: ${commit['sha']}, Status: ${status['status']}, Context: ${status['context']}, URL: ${status['target_url']}"
  }
}
```

### Creating a Commit Status against arbitrary commits
```groovy
for (commit in pullRequest['commits']) {
  createStatus(status: 'pending')
}
```

### Listing a Pull Request's current statuses
```groovy
for (status in pullRequest['statuses']) {
  echo "Commit: ${pullRequest['head']}, Status: ${status['status']}, Context: ${status['context']}, URL: ${status['target_url']}"
}
```
