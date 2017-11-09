# SLM-Env
Unity environment binaries for [SLM-Lab](https://github.com/kengz/SLM-Lab), built from [kengz/ml-agents](https://github.com/kengz/ml-agents).

If you're just using prebuilt environments for the Lab, just install the released binaries via `yarn`: e.g. `yarn add slm-env-3dball`.

This repository hosts the built Unity environment binaries released to `npm`.

## Installation

You need this repo `SLM-Env` and the builder repo [`kengz/ml-agents`](https://github.com/kengz/ml-agents.git) (use the fork as opposed to `Unity/ml-agents`).

```shell
git clone https://github.com/kengz/SLM-Env.git
git clone https://github.com/kengz/ml-agents.git
```

Then follow the setup instruction and intro from `ml-agents` for Unity.

## Naming Convention

Since the binaries are committed to Github, released on `npm`, and used by SLM-Lab, follow the convention compatible to all of them.

- Unity raw assets can follow Unity convention: `CamelCase`, e.g. `3DBall`
- built binaries `env_name`: `kebab-case`, e.g. `3dball`
- git branch name the same as `env_name`: `kebab-case`, e.g. `3dball`
- `npm` package name prepended with `slm-env-`, e.g. `slm-env-3dball`

## Build Unity Environment

1. Build your Unity environment and commit asset source code to `ml-agents` repo. For the most part follow the [original doc](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Getting-Started-with-Balance-Ball.md#building-unity-environment). Remember the core settings:
    - `Player > Resolution and Presentation > Run in Background (checked)`
    - `Player > Resolution and Presentation > Display Resolution Dialog (Disabled)`
    - `Academy > Brain > External`

2. When ready to build binary, decide on an `env_name`, e.g. `3dball`. You may want to check on [`npm`](https://www.npmjs.com/) that the name `slm-env-3dball` is not already taken, so you can release.

3. Come to this `SLM-Env` repo, create a new git branch from `master`:
  ```shell
  cd SLM-Env
  git checkout master
  git checkout -b 3dball
  ```

4. Build these versions of binaries and save to `SLM-Env/build/`:
  - MacOSX version
    - make `Academy > Training Configuration` as follow (or leave as-is if smaller than `Inference Configuration`):
      - Width: 128
      - Height: 72
      - Quality Level: 0
      - Time Scale: 100
    - build directory: `SLM-Env/build/`
    - save name: `3dball`
  - Linux version
    - make `Training Configuration` same as MacOSX
    - `Headless Mode (checked)`
    - save name: `3dball`

Next, ready to release.

## Release

1. Open up `package.json` and update:
  - replace `envname` as proper: `"name": "slm-env-3dball",`
  - if this is an update, bump version. Default is `"version": "1.0.0",`

2. commit and push the new `build/` folder and `package.json`:
  ```shell
  git add build/
  git add package.json
  git commit -m 'add 3dball'
  git push --set-upstream origin 3dball
  ```

3. Release to `npm` (make sure you are logged in first, by `npm login`):
  ```shell
  npm publish
  ```

  Since the binaries are huge, `npm` will throw an error near the end of it. Just ignore that.
  ```shell
  npm ERR! registry error parsing json
  npm ERR! publish Failed PUT 403
  npm ERR! code E403
  npm ERR! You cannot publish over the previously published version 1.0.0. : slm-env-3dball
  ```
  It should be available on [npmjs.com](https://www.npmjs.com/), just search for your package `slm-env-3dball`.

4. Add the release to `SLM-Lab` for usage: `yarn add slm-env-3dball`
