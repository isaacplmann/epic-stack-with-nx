# Epic Stack with Nx

This repository shows one way to set up [Nx](https://nx.dev) in an [Epic Stack](https://www.epicweb.dev/epic-stack) app.

## Benefits of Nx for an Epic Stack App

Nx has a lot of features, but the most useful features for an Epic Stack app are [task pipelines](https://nx.dev/features/run-tasks#defining-a-task-pipeline) and [caching](https://nx.dev/features/cache-task-results).

### Task Pipelines

Out of the box, epic stack uses `run-s` and `run-p` in the `build` and `validate` scripts to make sure that tasks are run in the correct order.  But this is error prone.  Specifically, if you swap the line order `build:icons` and `build:remix` in the `package.json` file, your `build` command will now fail.  Also, if you run `npm run build:remix` by itself, the `build:icons` task is not automatically run.  Nx allows you to explicitly define the task dependencies for each individual task and automatically runs them for you, so the developer running the task doesn't have to think about all the other tasks they need to run first.

### Caching

If the input source files haven't changed, there's no reason that the result of a task like `build` or `lint` would ever change.  Nx can leverage that fact to save you from wasted work.

Without caching, `build` takes about 10 seconds. With caching it takes 0.5 seconds.
Without caching, `validate` takes about 27 seconds. With caching it takes 0.5 seconds.

These times are taken from the starter repository.  Actual time savings in real world apps will be much more.

## Add Nx to Your Repository

You can [add Nx to your own repository](https://nx.dev/recipes/adopting-nx/adding-to-monorepo) by running the `npx nx init` command:

```
npx nx@latest init
```

## Specific Changes to This Repository

To create this repository, I ran the `npx nx init` command and then updated some caching settings.  You can review the specific changes in [this commit](https://github.com/isaacplmann/epic-stack-with-nx/commit/23beba6d72cb51c4cef25ef7023eb0de1a93bf71).

There are a few changes to highlight:
1. `.nx` folder is added to `.gitignore` so the cache is not stored in Git
2. A blank `nx.json` file is created for future configuration
3. `nx` is added as a `devDependency`
4. Some package scripts have `nx exec --` prepended to them.  This allows you to either run `npm run build:icons` or `nx build:icons` and leverage the Nx cache in either case.
5. The `nx` property in `nx.json` contains all the configuration for the task pipeline (`dependsOn`) and caching (`inputs` and `outputs`)

Currently each task has `inputs` set to the default value - the entire repository. There would be a lot more value if we fine tuned the `inputs` for each task to just the files that might change that task. To learn more about setting more detailed  `inputs` read the guide to [Configure Inputs for Task Caching](https://nx.dev/recipes/running-tasks/configure-inputs).

## Possibility of Expansion

This repo is set up so that it will be easy to add an Nx plugin and quickly generate another application or library.

```
nx add @nx/remix
nx generate app second-app --directory=second-app
nx add @nx/react
nx generate library ui-components --directory=ui-components
```

These generated libraries would allow you to get more value out of Nx.
