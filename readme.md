# Local Project Setup
```
# Create environment config file.
cp config/.env.example config/.env

# Fill in appropriate environment values.
nano config/.env

# Install pip requirements.
pip install -r requirements.txt

# Apply migrations and sync database schema.
python manage.py migrate
```

To run the project:
```
python manage.py runserver_plus
```
To access the database:
```
python manage.py shell_plus
```
To run test suite:
```
python manage.py test
``` 
To add a new dependency to or update requirements, add the entry to requirements.in and run `pip-compile` to generate requirements.txt:
```
vim requirements.in  # Updating Python dependencies as needed
pip-compile --upgrade  # Generate requirements.txt with updated dependencies
```
To run style checks and desired formatters:
```
pre-commit run --all-files
```
If wish to install pre-commit as a pre-commit git hook, you can run (optional):
```
pre-commit install
```

# How To Use This Template
1. Reset the git history to start your project.
```
# reset the git history
rm -rf .git
git init
git branch -M main
git add .
git commit -m "init"
git remote add origin [REMOTE_URL]
git push -u origin main
```
2. Decide which optional features you'd like to use.
## Included Optional Features
There are a number of optional features that have been included in this sample.
The code related to each sits between comments
`# START_FEATURE feature_name` and `# END_FEATURE feature_name`.

Before using this codebase, follow these steps to configure your app: 

1. Choose which features you don't want to use and remove all of the code between the tags for that feature.  
2. Remove all of the `# START_FEATURE` and `# END_FEATURE` comments from the codebase.
3. Run `pip-compile`. 
4. Check the resulting `requirements.txt` file into source control.
   - If you are using the `django_react` feature, you should also add the lock file generated by your
preferred package manager -- `package-lock.json` for `npm` and `yarn.lock` for `yarn`.

The following is a list of tagged features in this repo:

```
elastic_beanstalk
recommended_production_security_settings
bootstrap_messages
sentry
crispy_forms
django_ses
django_social
django_react
django_storages
docker
debug_toolbar
pre-commit
```

The codebase also has a number of reference examples.  These are all marked with the comment:
```
# TODO: delete me; this is just a reference example
```

## Feature Descriptions


### Django-React (django_react)


#### Files

The following files and folders are only needed if `django_react` is a desired feature in your app and can be safely
deleted from projects which do not leverage the feature.

- `nwb.config.js`
- `package.json`
- `webpack-stats.json`
- `config/webpack_loader.py`
- `src/`

#### Additional Setup


When using this feature, make sure to install the Node.js requirements using the manager of your choice 
(either `npm install` or `yarn install` will work) before proceeding with development.


#### Special Consideration for Running


For development on localhost when using Django-React, you should run the following command in a separate terminal to
your standard `runserver` command.
    
- `nwb serve --no-vendor`

If you have configured everything correctly, you should see each command complete and notify you
that the project is ready to be viewed.

- If you include `nwb` as a dependency, you can use the locally-installed `nwb` by running `node_modules/.bin/nwb serve --no-vendor` instead of relying on a globally installed `nwb`.


#### Adding a new React component


In this paradigm, React components are compiled and injected into the standard Django template. This means we can take 
advantage of the built-in templating functionality of Django and, with a bit of elbow grease, use the power of React to
make those templates responsive.

`django-react-loader` uses the same basic pattern for any component:

1. First, ensure that the library is loaded in your template: `{% load django_react_components %}`
2. Next, ensure that you have rendered the React runtime bundle: `{% render_bundle 'runtime' %}`
   - Note that you only have to do this once per page where React components will be used. 
3. Finally, load your React component on the page. `{% react_component 'Component' %}`
    - You can add any number of props as named keywords, e.g. `{% react_component 'Home' id='home' prop1=value_from_context %}`
    - You can also choose to pass props as an object instead of individual kwargs, e.g. `{% react_component 'Hello' id='hello' props=sample_props %}`.

#### Preparing for deployment


The preferred option for deployment is to add the below compilation step to the deployment configuration rather than 
building it locally. However, if you wish to build the app locally:

- run `nwb build --no-vendor`. This will generate or replace a `webpack_bundles` folder in your `/static` folder 
  populated with the compiled React components. This then allows `collectstatic` to collect these static assets and 
  make them available via the usual static assets pipeline set up in the deploy configuration.


#### Other Notes


- If you use `nwb serve` in your local development environment, you may see a persistent XHR error in the console -- a 
request by the app to something like `http://localhost:8000/sockjs-node/info?t=123456789`. This is normal and will 
  not appear on production or otherwise affect the function of your app. It is an artifact of the context bending we are
  doing by placing a React component outside the context of its expected Node environment.

- Note that calling `nwb build` does not remove existing compiled data from your static folder -- it may be worth deleting
`/static/webpack_bundles` before running a build for a deploy, as otherwise your package may become heavier than it
needs to be.
   - If you find that the number of files collected by `python manage.py collectstatic` continues to grow, this may be
    a sign that you should consider deleting the generated files and the `staticfiles` directory and starting with a
     fresh `python manage.py collectstatic`. This is another reason to prefer adding a compilation step to your deployment
     pipeline rather than running it locally.
     

### Optional Settings

`MAINTENANCE_MODE`: Set this flag on a server environment to stop all user requests to the site, such as when you need to make substantial server updates or run a complex database migration.


# Deployment
As a default for web applications, we strongly recommend using Elastic Beanstalk. To create a new deployment, [set up your local AWS credentials](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html) e.g. ~/.aws/config,
and then

Use [eb-create-environment](https://github.com/zagaran/eb-create-environment/):
```
eb-create-environment
```
To update an existing deployment
```
eb deploy [ENVIRONMENT_NAME]
```
To SSH into a deployment

Use [eb-ssm](https://github.com/zagaran/eb-ssm/):
```
eb-ssm [ENVIRONMENT_NAME]
```

# sample-django-app todo

Write a setup script that does the following:

* prompts the user for a project name and replaces instances of `[PROJECT]` with that name
* prompts the user with each feature tag and removes the code between those feature tags or not
* runs pip-compile
* remakes migrations
