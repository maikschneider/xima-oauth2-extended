# XIMA OAuth2 Extended

This repository contains additional provider for [league/oauth2-client](https://github.com/thephpleague/oauth2-client). When installed as TYPO3 extension, it is possible to extend the [waldhacker/ext-oauth2-client](https://github.com/waldhacker/ext-oauth2-client) for on-the-fly user creation.

## New provider

* MicrosoftResourceProvider

## TYPO3 user creation

To create frontend or backend users from OAuth2 authentication, you can create your own ResourceResolver by implementing the `ResourceResolverInterface` and register it in the extension configuration:

```php

'EXTENSIONS' => [
    // your existing configuration of waldhacker/ext-oauth2-client
    'oauth2_client' => [
        'providers' => [
            'yourProviderId' => [
                'description' => 'Your provider',
                'implementationClassName' => 'Xima\XimaOauth2Extended\ResourceProvider\MicrosoftResourceProvider',
                ...
            ],
            'secondProviderId' => [
                'description' => 'Another provider'
                ...
            ]
        ]
    ],

    'xima_oauth2_extended' => [
        'oauth2_client_providers' => [
            // provider of waldhacker/ext-oauth2-client you want to extend
            'yourProviderId' => [
                'resolverClassName' => \Xima\XimaOauth2Extended\ResourceResolver\MicrosoftResourceResolver::class,
                'createBackendUser' => true,
                'createFrontendUser' => false,
                'defaultBackendUsergroup' => '1,3',
                'defaultFrontendUsergroup' => '',
                'imageStorageBackendIdentifier' => '1:/user_upload/oauth',
            ],
            'secondProviderId' => [
                'resolverClassName' => \Xima\XimaOauth2Extended\ResourceResolver\GenericResolver::class,
                'createBackendUser' => true,
                'createFrontendUser' => true,
                'defaultBackendUsergroup' => '',
                'defaultFrontendUsergroup' => '',
            ],
        ],
    ],
]

```

## Available resolver

* GenericResolver
* MicrosoftResourceResolver
* GitlabResourceResolver

## FAQ

### Register Return-URLs

For the backend login the return url looks like this:

```
https://domain.de/typo3/login?loginProvider=1616569531&oauth2-provider=yourProviderId&login_status=login&commandLI=attempt
```

Replace `domain.de` and `yourProviderId` with your data!

### Login not working

Make sure `cookieSameSite` is set to `lax`.

```php
$GLOBALS['TYPO3_CONF_VARS']['BE']['cookieSameSite'] = 'lax';
$GLOBALS['TYPO3_CONF_VARS']['FE']['cookieSameSite'] = 'lax';
```

### Order of login provider

To change the order of provider displayed at the `/typo3` login page (OAuth login over classic username/password), use the following snippet:

```php
$GLOBALS['TYPO3_CONF_VARS']['EXTCONF']['backend']['loginProviders']['1616569531']['sorting'] = 75;
```

### Usage in TYPO3v12

The TYPO3 extension [waldhacker/ext-oauth2-client](https://github.com/waldhacker/ext-oauth2-client) is not yet ready for v12. However, there is a feature branch that is almost working - [this fork](https://github.com/maikschneider/ext-oauth2-client/tree/feature/v12-compatibility-1) makes the trick. To use it, adjust your `composer.json`:

```json
{
  "repositories": [
    {
      "url": "https://github.com/maikschneider/ext-oauth2-client.git",
      "type": "git"
    }
  ],
  "require": {
    "waldhacker/typo3-oauth2-client": "dev-feature/v12-compatibility-1"
  }
}
```
