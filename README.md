#Grunt Setup

Just some set up instructions for getting a project started with Grunt.

Set up your file structure. Here's what my file structure looks like when I begin a new grunt project. You can use this as an example. Please note that Grunt will install files in your project directory, so this will change if everything is working.

```
project-root-directory
>index.html
>gruntfile.js
>css
>js
>src
>>js
>>>scripts.js
>>scss
>>>layout
>>>>main.scss
```

You'll be editing the .js and .scss files in your src directory, but every time you run grunt your code will compile in /js or /css folders. Basically you'll set up your project just like you would a normal static site, but everything you actually edit will be nested in src.

Create your package.json file. The best way to do this is to go in to your project directory and type. ``` npm init ``` . Now fill out each field through the utility describing your project name, git repositories, and your npm dependencies. Now in your project directory run ```npm install grunt --save-dev```

Create your gruntfile.js at the root of your project directory. This file will load plugins and register tasks and configure tasks. It's kind of like a router. Here's a skeleton you can use for your gruntfile:

```
module.exports = function(grunt) {

  // Configure tasks(s)
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
  });

  // Load the plugins
  grunt.loadNpmTasks( );

  // Register task(s)
  grunt.registerTask('default', []);


};
```

Now let's review adding tasks. Here's an overview of how to add a task:

1. Install the plugin: ```npm install <plugin-name> --save-dev```
2. Add a configuration to gruntfile.js
3. Load plugin in gruntfile.js
4. Register the task in gruntfile.js

As an example of a task we can add, let's uglify our JS.

In the command line, type ```npm install grunt-contrib-uglify --save-dev```

Now configure your gruntfile.js by adding this to the block where tasks are configured:

```
// Configure tasks
grunt.initConfig({
  pkg: grunt.gile.readJSON('package.json'),
  uglify: {
    build: {
      src: 'src/js/*.js',
      dest: 'js/script.min.js'
    }
  }
});
```

Great. Now load the plugin:
```
//Load the plugins
grunt.loadNpmTasks('grunt-contrib-uglify');
```

Now register the task:
```
//Register tasks
grunt.registerTask('default', ['uglify:build']);
```

Now you're ready to test to see if your project is set up correctly by writing some js in a scripts.js file in src/js and then saving and running ```grunt```. If grunt is working, you'll get a minified JS file in root/js called scripts.min.js.

If you want to set up a task as a build task, you can set up your registerTask like this:
```
//Register tasks
grunt.registerTask('default', ['uglify:build']);
grunt.registerTask('build', ['uglify:build']);
```
Now only the tasks that are in the position of the first parameter ('build', where 'default' was) will run when you run ```grunt build```.

Now lets add our development task to the Config block.

```
// Configure tasks
grunt.initConfig({
  pkg: grunt.gile.readJSON('package.json'),
  uglify: {
    build: {
      src: 'src/js/*.js',
      dest: 'js/script.min.js'
    },
    dev: {
      options: {
        beautify: true,
        mangle: false,
        compress: false,
        preserveComments: 'all'
      },
      src: 'src/js/*.js',
      dest: 'js/script.min.js'
    }
  }
});
```
And now change your default in registerTask to reflect the new dev task you just added:
```
//Register tasks
grunt.registerTask('default', ['uglify:dev']);
grunt.registerTask('build', ['uglify:build']);
```

If you set your project up like this, you'll be able to enjoy concatenation without minification when you run a default (dev) task, but if you run a build task, you'll also get minification.

Lets set up a task watcher now.

```
npm install grunt-contrib-watch --save-dev
```

Now lets add our watch task to the Config block.

```
// Configure tasks
grunt.initConfig({
  pkg: grunt.gile.readJSON('package.json'),
  uglify: {
    build: {
      src: 'src/js/*.js',
      dest: 'js/script.min.js'
    },
    dev: {
      options: {
        beautify: true,
        mangle: false,
        compress: false,
        preserveComments: 'all'
      },
      src: 'src/js/*.js',
      dest: 'js/script.min.js'
    }
  },
  watch: {
    js: {
      files: ['src/js/*.js'],
      tasks: ['uglify:dev']
    }
  }
});
```

Load the plugin using the loadNpmTasks method:

```
//Load the plugins
grunt.loadNpmTasks('grunt-contrib-uglify');
grunt.loadNpmTasks('grunt-contrib-watch');
```

And contrary to usual practice you don't need to register watch. Instead you just run ```grunt watch``` in your project directory.

##Using SASS

There are two versions of SASS: grunt-contrib-sass - which is slower, needs Ruby, but is more up to date - and grunt-sass, which is easier to work with but not up to date.

```
npm install grunt-sass --save-dev
```

Now add SASS to your gruntfile:

```
// Configure tasks
grunt.initConfig({
  pkg: grunt.gile.readJSON('package.json'),
  uglify: {
    build: {
      src: 'src/js/*.js',
      dest: 'js/script.min.js'
    },
    dev: {
      options: {
        beautify: true,
        mangle: false,
        compress: false,
        preserveComments: 'all'
      },
      src: 'src/js/*.js',
      dest: 'js/script.min.js'
    }
  },
  sass: {
    dev: {
      options: {
        outputStyle: 'expanded'
      },
      files: {
        'css/styles.css' : 'src/css/application.scss'
      }
    }
  },
  watch: {
    js: {
      files: ['src/js/*.js'],
      tasks: ['uglify:dev']
    }
  }
});
```
Load the plugin using the loadNpmTasks method:

```
//Load the plugins
grunt.loadNpmTasks('grunt-contrib-uglify');
grunt.loadNpmTasks('grunt-contrib-watch');
grunt.loadNpmTasks('grunt-sass');
```

Now register the task as a dev task:

```
//Register tasks
grunt.registerTask('default', ['uglify:dev', 'sass:dev']);
grunt.registerTask('build', ['uglify:build']);
```

Finally add CSS to the watch configuration:
```
...

watch: {
  js: {
    files: ['src/js/*.js'],
    tasks: ['uglify:dev']
  },
  css: {
    files: ['src/scss/**/*.scss'],
    tasks: ['sass:dev']
  }
}

...
```

Now lets add a build task for sass. I'm going to just give you the entire gruntfile below:

```
// Configure tasks
grunt.initConfig({
  pkg: grunt.gile.readJSON('package.json'),
  uglify: {
    build: {
      src: 'src/js/*.js',
      dest: 'js/script.min.js'
    },
    dev: {
      options: {
        beautify: true,
        mangle: false,
        compress: false,
        preserveComments: 'all'
      },
      src: 'src/js/*.js',
      dest: 'js/script.min.js'
    }
  },
  sass: {
    dev: {
      options: {
        outputStyle: 'expanded'
      },
      files: {
        'css/styles.css' : 'src/css/application.scss'
      }
    },
    build: {
      options: {
        outputStyle: 'compressed'
      },
      files: {
        'css/styles.css' : 'src/css/application.scss'
      }
    }
  },
  watch: {
    js: {
      files: ['src/js/*.js'],
      tasks: ['uglify:dev']
    },
    css: {
      files: ['src/scss/**/*.scss'],
      tasks: ['sass:dev']
    }
  }
});
```

Now if you edit your scss and then save and run ```grunt build``` followed by ```sass build```, your application.scss file should compile to your styles.css as minified css!

##More Resources
SASS documentation: http://sass-lang.com/
Grunt documentation: http://gruntjs.com/getting-started
This tutorial by Dave McFarland is long, but awesome: https://teamtreehouse.com/library/up-and-running-with-grunt
