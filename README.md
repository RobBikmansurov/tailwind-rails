https://dev.to/davidmles/setting-up-rails-6-1-tailwind-css-2-2-with-jit-5415

```
rails new tailwind-jit-rails
cd tailwind-jit-rails
yarn add @fullhuman/postcss-purgecss@^4 postcss@^8 postcss-loader@^4 autoprefixer@^10 tailwindcss@^2

mkdir app/javascript/stylesheets
```
vi app/javascript/stylesheets/application.scss

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```

vi app/javascript/packs/application.js

```
...
import (channels)
import "stylesheets/application.scss"
...
```

vi postcss.config.js
```
require('tailwindcss'),
require .....
```
vi app/views/layouts/application.html.erb

```
<%= stylesheet_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
<%= stylesheet_link_tag......

```

create the Tailwind CSS configuration file:
npx tailwindcss init

Enable JIT mode and configure the purge option so that it looks like this:
```
mode: 'jit',
purge: [
  './app/views/**/*.html.erb',
  './app/helpers/**/*.rb',
  './app/javascript/**/*.js',
],
```

To prevent Webpacker from generating a rather annoying warning message, open babel.config.js and add this at the bottom, just before the closing ].filter(Boolean):
```
,
['@babel/plugin-proposal-private-methods', { loose: true }],
['@babel/plugin-proposal-private-property-in-object', { loose: true }]
```

Finally, to get Webpacker 5 to work with PostCSS 8 we need to add a small hack. Open config/webpack/environment.js and add this before module.exports = environment:
```
// https://github.com/rails/webpacker/issues/2784#issuecomment-737003955
function hotfixPostcssLoaderConfig (subloader) {
  const subloaderName = subloader.loader
  if (subloaderName === 'postcss-loader') {
    if (subloader.options.postcssOptions) {
      console.log(
        '\x1b[31m%s\x1b[0m',
        'Remove postcssOptions workaround in config/webpack/environment.js'
      )
    } else {
      subloader.options.postcssOptions = subloader.options.config;
      delete subloader.options.config;
    }
  }
}

environment.loaders.keys().forEach(loaderName => {
  const loader = environment.loaders.get(loaderName);
  loader.use.forEach(hotfixPostcssLoaderConfig);
});
```

rails g controller Home index

vi config/routes.rb
```
root 'home#index'
```

 vi app/views/home/index.html.erb 
```
<h1 class="font-bold text-4xl text-center text-red-500">Hello World!</h1>
```




Next to learn
https://larainfo.com/blogs/tailwind-css-simple-responsive-image-gallery-with-grid
https://larainfo.com/blogs/tailwind-css-simple-ecommerce-checkout-page-ui-example

