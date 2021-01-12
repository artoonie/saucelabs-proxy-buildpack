# SauceLabs Connect buildpack for Heroku.

Downloads SauceLabs Connect and places the `sc` binary in your root directory.

To make this work, set the environment variables `$SAUCE_USERNAME` and `$SAUCE_ACCESS_KEY`, and add the `tunnelIdentifier` query capability.

## Example: Setting Capability in Python

```python3
username = os.environ['SAUCE_USERNAME']
access_key = os.environ['SAUCE_ACCESS_KEY']
heroku_run_id = os.environ['HEROKU_TEST_RUN_ID']
url = f'https://{username}:{access_key}@ondemand.saucelabs.com:443/wd/hub'

capabilities = {
  'build' = heroku_run_id,
  'tunnelIdentifier': f'sc-proxy-tunnel-{heroku_run_id}' # This is the important line
}

webdriver.Remote(desired_capabilities=capabilities, command_executor=url)
```

## Example: Managing SauceConnect lifetime

You must ensure you start `sc` before your scripts begin, and kill `sc` when your scripts finish.

The following script will match the exit status of `./runtest.sh`, but sets a trap to ensure `sc` is cleaned up. Without the trap, the tests will hang waiting for `sc` to exit.

```bash
set -e

# Start tunnel, make sure its killed on exit (success or failure)
./sc -u $SAUCE_USERNAME -k $SAUCE_ACCESS_KEY -i sc-proxy-tunnel-$HEROKU_TEST_RUN_ID &
SC_PID=$!
trap "kill $SC_PID" EXIT

./runtest.sh
```
