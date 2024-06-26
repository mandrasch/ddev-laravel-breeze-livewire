# ddev-laravel-breeze-livewire

**This repository is based on https://github.com/mandrasch/ddev-laravel-vite**

Just a fresh installation of Laravel Breeze with Laravel Livewire v3.

## Local setup after clone

```
ddev composer install && ddev npm install && ddev artisan key:generate
```

Run it:

```
ddev launch && ddev npm run dev
```

## How was this created?

1. Followed https://ddev.readthedocs.io/en/latest/users/quickstart/#laravel

```
ddev config --project-type=laravel --docroot=public --create-docroot --php-version=8.1
ddev composer create --prefer-dist --no-install --no-scripts laravel/laravel -y
ddev composer install
ddev artisan key:generate
```

2. Install Laravel Breeze (https://laravel.com/docs/10.x/starter-kits)

```
ddev composer require laravel/breeze --dev
ddev artisan breeze:install
# Selected: Livewire with Alpine, dark mode - yes, test: pest
```

3. Run migrations, install dependencies:

```
ddev artisan migrate
ddev npm install
```

4. Add support for vite within DDEV

Add this to `.ddev/config.yaml` to expose the vite port:

```yaml 
# .ddev/config.yaml
web_extra_exposed_ports:
  - name: node-vite
    container_port: 5173
    http_port: 5172
    https_port: 5173
```

⚠️  A `ddev restart` is needed after that.

The [`vite.config.js`](https://github.com/mandrasch/ddev-laravel-breeze-livewire/blob/main/vite.config.js) needs some modifications as well. 

These `.server` options were added:

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

const port = 5173;
const origin = `${process.env.DDEV_PRIMARY_URL}:${port}`;

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
    server: {
        // respond to all network requests
        host: '0.0.0.0',
        port: port,
        strictPort: true,
        // Defines the origin of the generated asset URLs during development,
        // this will also be used for the public/hot file (Vite devserver URL)
        origin: origin
    }
});
```

## Further resources

- Connect with the DDEV community on [Discord](https://discord.gg/hCZFfAMc5k)
- More experiments and info about DDEV + vite: https://my-ddev-lab.mandrasch.eu/

Thanks to the DDEV maintainers and DDEV open source community! 💚
