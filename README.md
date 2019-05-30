# NPM - *is not recognized as an internal or external command, operable program or a batch file*

This error is one you can run into for multiple reason with NPM.
The most obvious reason this can happen is that you have forgotten to install a global but are using it in your NPM script, e.g.:

```json
  "scripts": {
    "start": "ts-node index.ts"
  }
```

If you have forgotten to install `ts-node` using `npm install -g ts-node`, you will run into a problem as `ts-node` is not in `%PATH%`.

Globals are less common these days because NPX exists which allows you to use a global when you need it without cluttering up your PATH
or having to worry about versions (you always get latest), but also because NPM is now smart enough to know to run a package in
`dependencies` or `devDependencies` if there is one that matches the name of the thing it is asked to invoke.

So, the first solutions might be to install a missing global or to run `npm install` to ensure everything is correctly present in
`node_modules` (with or without deleting an existing broken copy first). This might fix it.

But! What if it doesn't?

I have run into this problem recently when I have manually cleared up duplicate entries in my `%PATH%` (I think it was the user version)
and after the next reboot, I have started to experience this problem.

What makes no sense though is that for me this is happening with a local package (`react-scripts` when running `npm start` in CRA) so why
would `%PATH%` be a problem? My NPM is still the same version after the reboot, so if it knew how to invoke packages before the reboot,
why does it not know now?

I don't know.

But I am pretty sure the problem is with PATH. Not sure how removing duplicate entries could have broken it, and not sure how it broke a
local package which was not in PATH to beging with, but now after each reboot, NPM and Node work just fine, but executing local packages
from NPM `scripts` does not work. Not even after `npm install` (but then again `node_modules` does not change across reboots so why would
that matter).

I am using NVM which takes care of PATH for me as it switches Node and NPM versions back and forth while asked.
I can't fix this PATH mess by hand, my hand is what broke it in the first place, so maybe NVM for Windows can save me?

The first time this happened, I solved it by reinstalling NVM for Windows. Later I figured I might be able to get away with just doing
`nvm use` again for the same version - didn't help, or switching back and forth between a couple Node and NPM versions to land on the
original one and NVM would fix the PATH in the process - but that didn't work either.

So here I am, with the only know solution to make this work being to reinstall NVM for Windows after every reboot and then install Node
through it and then it magically starts to work. You can see why I suspect this is a user PATH issue or something like that, because it
does not survive the reboot (which user PATH generally does but maybe something wonky is going on with working around its length issue
which makes it unstable across reboots).

I thought maybe the solution is to install NVM for all users (I am the only user of this computer), but there is no such option in the
installer. So for now, the reinstall-NVM solution works and I will have to do this monkey dance after each reboot. Which is not that
often so it's not bothersome enough to actually solve it yet.

Uninstalling and installing using NVM again changes the binary, but still won't work.

I tried to uninstall NVM completely and instead just install LTS Node. That worker after restarting VS Code, but it remaings to be seen
whether it will still work after a restart. And this is yet another indication that has to do with PATH, because restarting VS Code
refreshes the internal terminal environment variables I think.

---

Updates! This is actually a problem with… VS Code! How, I still don't know, but: when this error runs in the VS Code terminal, but
you then open PowerShell proper and run the same command there, it works! Amazingly, `node -v`, `npm -v` and `get-command npm` are
all equivalent. I'm at loss at what could be causing this and I _still_ don't know how to fix the PowerShell in the VS Code terminal
so that it picks up the working NPM without reinstalling Node/NPM/NVM, but at least now I have a way to avoid 15 minutes of reinstalling
shit every morning.

I have dumped `$Env:Path` in the VS Code terminal and in PowerShell to two different files and indeed the VS Code terminal one is _way_
different. It has many many more entries and a load of duplicates, too.

I created a VS Code issue for this: https://github.com/microsoft/vscode/issues/74606
