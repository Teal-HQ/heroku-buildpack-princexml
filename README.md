# Heroku Buildpack PrinceXML

A buildpack that installs PrinceXML (version `20240222`) on Heroku.

## Usage

This buildpack will not work on its own. It is meant to be used as part of Heroku's [multiple buildpack support](https://devcenter.heroku.com/articles/using-multiple-buildpacks-for-an-app).

This buildpack downloads a PrinceXML package, installs it, configures a license (if available), and creates a wrapper script where your application can find it.

After deploying an application, you can verify `prince` is available like so:

1. `heroku run bash` from your project directory, or `heroku run bash -a <your_app_name>`
1. `which prince` should report `/app/bin/prince`
1. `prince --version` should report `20240222`
1. `prince -h` should display usage information

If invoking `prince` fails, you may have unmet dependencies. See the next section for setting those up.

You may wish to create a simple test HTML file and try out `prince`:

```bash
$ cat >"tmp/test.html" <<EOF
<html>
<head></head>
<body>
<p>
Hello from Prince
</p>
</body>
</html>
EOF
$ prince tmp/test.html
$ ls tmp/test.pdf
```

## Dependencies

PrinceXML depends on some shared libraries which may not ship with your Heroku stack. For example, PrinceXML `20240222` on the Heroku-22 stack needs `libgif-dev` and `libavif-dev`.

We recommend using [heroku-buildpack-apt](https://elements.heroku.com/buildpacks/heroku/heroku-buildpack-apt), adding these dependencies to an `Aptfile` in your application. Be sure to include the `heroku-buildpack-apt` buildpack prior to `heroku-buildpack-princexml`.

## PrinceXML License

To use a Prince license, remove all carriage returns from the Prince license.dat XML file and copy/paste the string into a Heroku environment variable named PRINCE_LICENSE. Note that the buildpack itself generates the license file from the ENV variable, so you will need to re-deploy your app to generate it.
