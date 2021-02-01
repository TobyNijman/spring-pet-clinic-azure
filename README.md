# spring-pet-clinic-azure
https://github.com/spring-petclinic

# installing Azurez CLI
https://docs.microsoft.com/nl-nl/cli/azure/install-azure-cli-macos

# azure pipeline cache
https://docs.microsoft.com/en-us/azure/devops/pipelines/release/caching?view=azure-devops

The Cache task has two required inputs: key and path.

## Path input
path should be set to the directory to populate the cache from (on save) and to store files in (on restore). It can be absolute or relative. Relative paths are resolved against $(System.DefaultWorkingDirectory).
## Key input
key should be set to the identifier for the cache you want to restore or save. Keys are composed of a combination of string values, file paths, or file patterns, where each segment is separated by a | character.
* Strings:
fixed value (like the name of the cache or a tool name) or taken from an environment variable (like the current OS or current job name)

* File paths:
path to a specific file whose contents will be hashed. This file must exist at the time the task is run. Keep in mind that any key segment that "looks like a file path" will be treated like a file path. In particular, this includes segments containing a .. This could result in the task failing when this "file" does not exist.

* File patterns:
comma-separated list of glob-style wildcard pattern that must match at least one file. For example:

```**/yarn.lock```: all yarn.lock files under the sources directory
```*/asset.json, !bin/**```: all asset.json files located in a directory under the sources directory, except under the bin directory

```
variables:
  MAVEN_CACHE_FOLDER: $(Pipeline.Workspace)/.m2/repository
  MAVEN_OPTS: '-Dmaven.repo.local=$(MAVEN_CACHE_FOLDER)'

steps:
- task: Cache@2
  inputs:
    key: 'maven | "$(Agent.OS)" | **/pom.xml'
    restoreKeys: |
      maven | "$(Agent.OS)"
      maven
    path: $(MAVEN_CACHE_FOLDER)
  displayName: Cache Maven local repo
```
In this example the key is the static string "maven", the OS the job is running on and hash of the pom.xml.
The cache will be created from $(Pipeline.Workspace)/.m2/repository

Note: If you are using a Maven task, make sure to also pass the MAVEN_OPTS variable because it gets overwritten otherwise:
```
task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
    mavenOptions: '-Xmx3072m $(MAVEN_OPTS)'
```

# TODO
1. use buildpacks and publish image to azure
2. setup kurbernetes cluster using kustomize or helm
3. deploy to kubernetes cluster
4. Deploy angular FE https://github.com/spring-petclinic/spring-petclinic-angular
5. seperate db instead of in memory
