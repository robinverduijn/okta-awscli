# okta_awscli - Retrieve AWS credentials from Okta

Authenticates a user against Okta and then uses the resulting SAML assertion to retrieve temporary STS credentials from AWS.

This project is largely inspired by https://github.com/nimbusscale/okta_aws_login, but instead uses a purely API-driven approach, instead of parsing HTML during the authentication phase.

Parsing the HTML is still required to get the SAML assertion, after authentication is complete. However, since we only need to look for the SAML assertion in a single, predictable tag, `<input name="SAMLResponse"...`, the results are a lot more stable across any changes that Okta may make to their interface.


## Installation

- `pip install okta-awscli`
- Configure okta-awscli via the `~/.okta-aws` file with the following parameters:

```
[default]
base-url = <your_okta_org>.okta.com

## The remaining parameters are optional.
## You will be prompted for them, if they're not included here.
username = <your_okta_username>
password = <your_okta_password> # Only save your password if you know what you are doing!
factor = <your_preferred_mfa_factor> # Current choices are: GOOGLE or OKTA
factor-type = <your_preferred_mfa_type> # Set to "push" to enable Okta push notifications, or leave out
```

## Supported Features

- Tenant wide MFA support
- Okta Verify [Play Store](https://play.google.com/store/apps/details?id=com.okta.android.auth) | [App Store](https://itunes.apple.com/us/app/okta-verify/id490179405)
- Okta Verify Push Support
- Google Authenticator [Play Store](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2) | [App Store](https://itunes.apple.com/us/app/google-authenticator/id388497605) 


## Unsupported Features

- Per application MFA support


## Usage

`okta-awscli --profile <aws_profile> <awscli action> <awscli arguments>`
- Follow the prompts to enter MFA information (if required) and choose your AWS app and IAM role.
- Subsequent executions will first check if the STS credentials are still valid and skip Okta authentication if so.
- Multiple Okta profiles are supported, but if none are specified, then `default` will be used.


### Example

`okta-awscli --profile my-aws-account iam list-users`

If no awscli commands are provided, then okta-awscli will simply output STS credentials to your credentials file, or console, depending on how `--profile` is set.

Optional flags:
- `--profile` Sets your temporary credentials to a profile in `.aws/credentials`. If omitted, credentials will output to console.
- `--force` Ignores result of STS credentials validation and gets new credentials from AWS. Used in conjunction with `--profile`.
- `--verbose` More verbose output.
- `--debug` Very verbose output. Useful for debugging.
- `--cache` Cache the acquired credentials to ~/.okta-credentials.cache (only if --profile is unspecified)
- `--okta-profile` Use a Okta profile, other than `default` in `.okta-aws`. Useful for multiple Okta tenants.
- `--token` or `-t` Pass in the TOTP token from your authenticator
