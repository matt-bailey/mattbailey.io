---
layout: post
title: "A Beginner's Guide To Grunt: Redux"
---

<p><img alt="image" src="/images/dist/tumblr_inline_mjrobcZQpo1qz4rgp.png"/></p>

<p class="lead">Back in March 2013 I wrote <a href="http://mattbailey.io/a-beginners-guide-to-grunt/" title="Matt Bailey: A Beginner's Guide To Grunt">A Beginner's Guide To Grunt</a>, and it's become the most visited article on my site. I wrote it at a time when I was just starting out with <a href="http://gruntjs.com/" title="Grunt">Grunt</a>, and it was as much a guide for myself as anyone else. Now, 18 months later, I felt it was time to revisit how I use Grunt because I've learned a lot more in that time.</p>

**If you're itching to just get the code it's all [here on Github](https://github.com/matt-bailey/grunt-frontend-boilerplate).**

**Update: 3 December, 2014 - I've added `jshint` and `watch` tasks to the article and git repo.**

## Install Node and Grunt globally

First of all you will need to make sure you have [Node](http://nodejs.org/download/) and [Grunt CLI](http://gruntjs.com/getting-started) (command line interface) installed.

- The Node site has various downloadable packages for different systems. [Full details can be found here](http://nodejs.org/download/).
- Once you've installed Node simply run the following command in your terminal of choice (I use [iTerm2](http://iterm2.com/)) to install **grunt-cli**:

```bash
npm install -g grunt-cli
```

## Install Ruby and Sass

**Update:** I've recently switched from using `grunt-contrib-sass` to `grunt-sass` which uses the faster, but experimental [libsass](http://libsass.org/) C++ compiler. As such, it doesn't require Ruby or the Sass gem. However, if you experience issues when compiling with `grunt-sass` you should probably use `grunt-contrib-sass`, but in that case you will need to install Ruby and the Sass gem.

~~I use Sass as my CSS preprocessor. In order to use the Sass Grunt task you will need to install Ruby ([full installation instructions can be found here](https://www.ruby-lang.org/en/installation/)) and, once you've done that, the [Sass](http://sass-lang.com/download.html) gem:~~

```bash
gem install sass
```

## Create the project directories

Our project requires a couple of directories to be set up. Mirror the structure below:

```
grunt/
src/
src/images/
src/scripts/
src/styles/
```

## Create a Gruntfile

First of all, I don't use 'scaffolding' tools any more (such as `grunt init` or [Yeoman](http://yeoman.io/)). I set up everything from scratch, which means I have a much greater understanding of what's going on now. It's really not that hard once you've done it a few times.

In the root of your project create a file called `Gruntfile.js`.

In that file add the following code:

```javascript
module.exports = function(grunt) {

    require('time-grunt')(grunt);

    require('load-grunt-config')(grunt, {
        jitGrunt: true
    });
};
```

Believe it or not, that's it as far as our Gruntfile is concerned!

`time-grunt` tells you how much time each task and the total build has taken, and `jitGrunt: true` tells `load-grunt-config` to use the faster [jit-grunt](https://github.com/shootaroo/jit-grunt) (Just In Time) task loader (this is optional, but speed is good right?).

## Create a package file

Let's move on and also create our basic `package.json` file. This file will shortly contain our project's dependencies. Add the following (obviously change references to 'my project' to the actual name of your project):

```javascript
{
  "name": "my-project",
  "version": "0.0.1",
  "description": "My project"
}

```

## Add some dependencies

Now we have all we need to be able to start adding some modules. Run each of the lines of code below, one after the other:

```bash
npm install grunt --save-dev
npm install time-grunt --save
npm install load-grunt-config --save-dev
npm install grunt-concurrent --save-dev
npm install grunt-contrib-clean --save-dev
npm install grunt-contrib-imagemin --save-dev
npm install grunt-sass --save-dev
npm install grunt-contrib-uglify --save-dev
npm install grunt-contrib-jshint --save-dev
npm install jshint-stylish --save
npm install grunt-contrib-watch --save-dev
```

If you look in `package.json` you should now see something like this:

```javascript
{
  "name": "my-project",
  "version": "0.0.1",
  "description": "My Project",
  "devDependencies": {
    "grunt": "^0.4.5",
    "grunt-concurrent": "^1.0.0",
    "grunt-contrib-clean": "^0.6.0",
    "grunt-contrib-imagemin": "^0.8.1",
    "grunt-contrib-jshint": "^0.10.0",
    "grunt-contrib-uglify": "^0.6.0",
    "grunt-contrib-watch": "^0.6.1",
    "grunt-sass": "^0.16.1",
    "load-grunt-config": "^0.13.1"
  },
  "dependencies": {
    "jshint-stylish": "^1.0.0",
    "time-grunt": "^1.0.0"
  }
}
```

Here is a summary of what we've just installed:

1. `grunt`: The task runner itself.
- `time-grunt`: This isn't required, but it's a neat addition - it tells you how much time each task and the total build has taken.
- `load-grunt-config`: Allows you to keep our main Gruntfile short and succinct. More on that in a bit.
- `grunt-concurrent`: Run tasks concurrently - Out-of-the-box Grunt will run each task one after the other, which can take a while depending on the amount and type of tasks you need to run. However, there are often tasks that are not dependent on other tasks which can be run at the same time.
- `grunt-contrib-clean`: Quite simply, this task deletes 'stuff' - use with caution!
- `grunt-contrib-imagemin`: Indispensible for all your image optimisation needs.
- `grunt-sass`: Compiles your SASS/SCSS files into CSS. **Please note:** This Sass task uses the faster, but more experimental [libsass](http://libsass.org/) compiler. If you experience problems you should probably use the more stable, but slower [grunt-contrib-sass](https://github.com/gruntjs/grunt-contrib-sass) task instead.
- `grunt-contrib-uglify`: Makes your Javascript nice and ugly.
- `grunt-contrig-jshint`: Validates your Javascript files.
- `jshint-stylish`: Totally optional, but it makes the `grunt-contrig-jshint` output look pretty.
- `grunt-contrib-watch`: Run tasks whenever watched files are changed.

## Configure the tasks

One of the best modules I've discovered is `load-grunt-config`. It allows us to put the config for each of our tasks in separate files, which is far more manageable than having everything in one long Gruntfile.

In the `grunt` directory create the following files:

```
grunt/aliases.yaml
grunt/concurrent.js
grunt/clean.js
grunt/imagemin.js
grunt/jshint.js
grunt/sass.js
grunt/uglify.js
grunt/watch.js
```

**Note: The names of these files must match the names of the tasks.**

Copy and paste the config for each task below into the relelvant file.

### aliases.yaml

```yaml
default:
  description: 'Default (production) build'
  tasks:
    - prod
dev:
  description: 'Development build'
  tasks:
    - 'concurrent:devFirst'
    - 'concurrent:devSecond'
img:
  description: 'Image tasks'
  tasks:
    - 'concurrent:imgFirst'
devimg:
  description: 'Development build and image tasks'
  tasks:
    - dev
    - img
prod:
  description: 'Production build'
  tasks:
    - 'concurrent:prodFirst'
    - 'concurrent:prodSecond'
    - img
```

What we're doing here is defining various aliases for our tasks:

- `default` - Runs the `prod` tasks when you run `grunt` on the command line.
- `dev` - Runs the development tasks (but not image tasks)
- `img` - Runs the image tasks
- `devimg` - Runs the development and image tasks
- `prod` - Runs the production and image tasks

**[Click here](https://github.com/firstandthird/load-grunt-config#aliases) for more information on configuring task aliases for `load-grunt-config`.**

### concurrent.js

```javascript
module.exports = {

    // Task options
    options: {
        limit: 3
    },

    // Dev tasks
    devFirst: [
        'clean',
        'jshint'
    ],
    devSecond: [
        'sass:dev',
        'uglify'
    ],

    // Production tasks
    prodFirst: [
        'clean',
        'jshint'
    ],
    prodSecond: [
        'sass:prod',
        'uglify'
    ],

    // Image tasks
    imgFirst: [
        'imagemin'
    ]
};
```

Taking the dev tasks as an example, you can see that they are set up to run `clean` first, and then `sass:dev` and `uglify` concurrently to regenerate the css and javascript.

**[Click here](https://github.com/sindresorhus/grunt-concurrent) for more information on configuring `grunt-concurrent`.**

### clean.js

```javascript
module.exports = {
    all: [
        "dist/"
    ]
};
```

Configuring `grunt-contrib-clean` is quite simple. Here I'm just removing all the contents of the `dist/` directory. Use this task with caution - it will indiscriminately delete whatever you tell it to without any warnings, so make sure you configure it correctly.

**[Click here](https://github.com/gruntjs/grunt-contrib-clean) for more information on configuring `grunt-contrib-clean`.**

### imagemin.js

```javascript
module.exports = {
    all: {
        files: [{
            expand: true,
            cwd: 'src/',
            src: ['images/*.{png,jpg,gif}'],
            dest: 'dist/'
        }]
    }
};
```

The config above simply optimises all images in `src/images/` and saves them to `dist/images/`.

**[Click here](https://github.com/gruntjs/grunt-contrib-imagemin) for more information on configuring `grunt-contrib-imagemin`.**

### sass.js

```javascript
module.exports = {
    // Development settings
    dev: {
        options: {
            outputStyle: 'nested',
            sourceMap: true
        },
        files: [{
            expand: true,
            cwd: 'src/styles',
            src: ['*.scss'],
            dest: 'dist/styles',
            ext: '.css'
        }]
    },
    // Production settings
    prod: {
        options: {
            outputStyle: 'compressed',
            sourceMap: false
        },
        files: [{
            expand: true,
            cwd: 'src/styles',
            src: ['*.scss'],
            dest: 'dist/styles',
            ext: '.css'
        }]
    }
};
```

I've split the Sass task into development and production workflows. The config is very similar, but for development purposes I've set the output style to `nested` and activated source maps.

**[Click here](https://github.com/sindresorhus/grunt-sass) for more information on configuring `grunt-sass`.**

### jshint.js

```javascript
module.exports = {

    options: {
        reporter: require('jshint-stylish')
    },

    main: [
        'src/scripts/*.js'
    ]
};
```

The jshint task validates your Javascript and makes sure everything is hunky-dory.

**[Click here](https://github.com/gruntjs/grunt-contrib-jshint) for more information on configuring `grunt-contrib-jshint`.**

### uglify.js

```javascript
module.exports = {
    all: {
        files: [{
            expand: true,
            cwd: 'src/scripts',
            src: '**/*.js',
            dest: 'dist/scripts',
            ext: '.min.js'
        }]
    }
};
```

The uglify task simply takes Javascript files and minifies them - simples!

**[Click here](https://github.com/gruntjs/grunt-contrib-uglify) for more information on configuring `grunt-contrib-uglify`.**

### watch.js

```javascript
module.exports = {

    options: {
        spawn: false,
        livereload: true
    },

    scripts: {
        files: [
            'src/scripts/*.js'
        ],
        tasks: [
            'jshint',
            'uglify'
        ]
    },

    styles: {
        files: [
            'src/styles/*.scss'
        ],
        tasks: [
            'sass:dev'
        ]
    },
};
```

Watch runs specified tasks whenever your watched files are changed in any way - added, edited, deleted.

**Note: The easiest way to get Livereload working is to install the [browser extension](http://feedback.livereload.com/knowledgebase/articles/86242).**

**[Click here](https://github.com/gruntjs/grunt-contrib-watch) for more information on configuring `grunt-contrib-watch` and Livereload.**

## Run the tasks

If you've finished setting up your project as outlined above then you can now run the tasks. As discussed earlier there are various task aliases you can run. For now just run `grunt` on the command line from the root of your project.

All being well you should see a load of text scroll up the screen and then finish with a message that looks something like this.

![Grunt Production Build Output](/images/dist/grunt-frontend-boilerplate-1.png)

I love the little summary that `time-grunt` provides. I can see how long each concurrent task-set took to run, plus how long the whole build process took - neat!

Depending on your requirements you could also choose to run `grunt dev`, `grunt devimg`, or `grunt img`.

You can also run `grunt watch` if you'd like grunt to watch for changes to your `.scss` and `.js` files and automatically run the `sass`, or `jshint` and `uglify` tasks.

## Summary

And that's about all there is to it really. If you experiment with the above you'll soon gain confidence, start adding new tasks and modifying the workflow to better suit your own requirements.

**Once again, the code accompanying this article can also be found [on Github](https://github.com/matt-bailey/grunt-frontend-boilerplate).**

Leave any questions you have in the comments below, or post issues on [github](https://github.com/matt-bailey/grunt-frontend-boilerplate/issues).
