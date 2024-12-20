# LARAVEL 11 + REVERB + REDHAT LINUX 9.5 + APACHE

Assuming you already installed and created a laravel project and added laravel reverb. This is the setup you need on an Apache Web server in Redhat Linux like Oracle Linux 9, Rocky Linux 9, Alma Linux 9.

## Under config/reverb.php

```shell
...
'reverb' => [
    'host' => env('REVERB_SERVER_HOST', '0.0.0.0'),
    'port' => env('REVERB_SERVER_PORT', 8080),
    'hostname' => env('REVERB_HOST'),
    'options' => [
        'tls' => [
            'verify_peer' => false,
            'allow_self_signed' => true,
            //'local_cert' => '/var/www/html/cacert.pem',
            //'cafile' => '/var/www/html/cacert.pem',
            'local_cert' => '/etc/ssl/certs/apache-selfsigned.crt',
            'local_pk' => '/etc/ssl/private/apache-selfsigned.key',
        ],
    ],
...
```

## Under config/broadcasting.php

```shell
'reverb' => [
    'driver' => 'reverb',
    'key' => env('REVERB_APP_KEY'),
    'secret' => env('REVERB_APP_SECRET'),
    'app_id' => env('REVERB_APP_ID'),
    'options' => [
        'host' => env('REVERB_HOST'),
        'port' => env('REVERB_PORT', 443),
        'scheme' => env('REVERB_SCHEME', 'https'),
        'useTLS' => env('REVERB_SCHEME', 'https') === 'https',
    ],
    'client_options' => [
        // Guzzle client options: https://docs.guzzlephp.org/en/stable/request-options.html
        'verify' => false,
    ],
    // for self signed ssl cert
    'curl_options' => [
        CURLOPT_SSL_VERIFYHOST => 0,
        CURLOPT_SSL_VERIFYPEER => 0
    ],
],
```

## Under .env

```shell
REVERB_APP_ID=451837
REVERB_APP_KEY=jbveh4ctpsv42yq0vlpp
REVERB_APP_SECRET=8sthylrs78cvu0razrew
REVERB_HOST="rise.diavox.net"
REVERB_PORT=8080
REVERB_SCHEME=https

VITE_REVERB_APP_KEY="${REVERB_APP_KEY}"
VITE_REVERB_HOST="${REVERB_HOST}"
VITE_REVERB_PORT="${REVERB_PORT}"
VITE_REVERB_SCHEME="${REVERB_SCHEME}"
```

## Under resources/js/echo.js

```js
import Echo from 'laravel-echo';

import Pusher from 'pusher-js';
window.Pusher = Pusher;

window.Echo = new Echo({
    broadcaster: 'reverb',
    key: import.meta.env.VITE_REVERB_APP_KEY,
    wsHost: import.meta.env.VITE_REVERB_HOST,
    wsPort: import.meta.env.VITE_REVERB_PORT ?? 80,
    wssPort: import.meta.env.VITE_REVERB_PORT ?? 443,
    forceTLS: (import.meta.env.VITE_REVERB_SCHEME ?? 'https') === 'https',
    enabledTransports: ['ws', 'wss'],
});
```
