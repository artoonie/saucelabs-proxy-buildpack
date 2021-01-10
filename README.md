# SauceLabs Connect buildpack for Heroku.

Downloads SauceLabs Connect and places the `sc` binary in your root directory.

To make this work, set the following environment variables:
```bash
$SAUCE_USERNAME
$SAUCE_ACCESS_KEY
```

and add the following Capability when starting. For example, in Python you might have something like:

```python3
username = os.environ['SAUCE_USERNAME']
access_key = os.environ['SAUCE_ACCESS_KEY']
heroku_run_id = os.environ['HEROKU_TEST_RUN_ID']
url = f'https://{username}:{access_key}@ondemand.saucelabs.com:443/wd/hub'
capabilities = {
  'build' = heroku_run_id,
  'tunnelIdentifier': f'sc-proxy-tunnel-{heroku_run_id}'
}
webdriver.Remote(desired_capabilities=capabilities, command_executor=url)
```

Then, when starting your script, make sure you call `sc` beforehand and kill it afterward, e.g.:
```bash
./sc -u $SAUCE_USERNAME -k $SAUCE_ACCESS_KEY -i sc-proxy-tunnel-$HEROKU_TEST_RUN_ID &
./runtest.sh
kill %
```
