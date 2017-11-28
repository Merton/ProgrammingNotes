# Django & Vue
## Django

**Useful Commands**

Set up a new project & structure
```
django-admin startproject mysite
```

Creating new apps
```
python manage.py startapp name_of_new_app
```

## Vue

use `npm install vue-cli` to install the command line interface for vue, which is useful for setting up what 
templates you want to use.

Then, in the directory you want vue to be in, run:
```
vue init 'choice-of-template' 'name-of-project'
```
The choice of templates are:

- webpack - A full-featured Webpack + vue-loader setup with hot reload, linting, testing & css extraction.

- webpack-simple - A simple Webpack + vue-loader setup for quick prototyping.

- browserify - A full-featured Browserify + vueify setup with hot-reload, linting & unit testing.

- browserify-simple - A simple Browserify + vueify setup for quick prototyping.

- pwa - PWA template for vue-cli based on the webpack template

- simple - The simplest possible Vue setup in a single HTML file

You can then follow the command prompts which allow you to set your project name, vue build etc.

The next steps are displayed in the terminal, but they are:
```
cd name-of-project
npm install
npm run dev
```

## Setting up django and Vue to work together
Set up Django API, and run it using `python manage.py runserver`
Set up Vue environment inside Django project.
The vue app will make ajax calls on the django API, using the package vue-axios. 
```
loadPubs: function () {
      this.status = 'Loading...'
      var vm = this;
      axios.get('http://127.0.0.1:8000/api/pubs/')
      .then(function (response) {
        console.log(response.data);
        vm.'data_object' = response.data;
      })
```

(**N.B** If in the future you want to extend what is defined in 'data_object' you need to be more specific when you assign the response.data field.)

However, this will be rejected as the ajax call doesn't contain a verification token (CSRF) because it's running in your local environment.
To get around this, use the package django-cors-headers for Django.

https://github.com/ottoyiu/django-cors-headers 

Add `corsheaders` to the list of Installed Apps in your django settings.py file.

Then, create this section (adapt the whitelisted addresses as you need):
```
CORS_ORIGIN_WHITELIST = (
    'localhost:8080',
    '127.0.0.1:8080'
)
```
Vues default port is 8080, so this says dont require verification from that port.
