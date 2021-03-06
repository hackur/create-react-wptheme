# Create React WP Theme

The intention of this project is to maintain a set of custom `react-scripts` that will allow you to
create React WordPress themes as easily as `create-react-app` allows other devs to create their apps.

The biggest difference with this project and the original is that **it uses your WordPress server as the
development server instead of the Webpack Dev Server.**

## Usage

To create a WordPress theme using the `create-react-wptheme`, follow these steps.

-   Make sure your WordPress server is up and running.
-   Change dir into your WordPress themes folder (**this is just an example, use your real themes folder**).
    -   Windows: `cd C:\xampp\htdocs\wordpress\wp-content\themes`
    -   Mac or \*nix: `cd /xampp/htdocs/wordpress/wp-content/themes`
-   Use `npx create-react-wptheme` to make a new theme
    -   For example: (**replace "my_react_theme" with whatever you want your theme to be named**):
    -   `npx create-react-wptheme my_react_theme`
-   When it finishes it'll tell you to change into your new theme's folder and run the Nodejs watcher (replace "my_react_theme" with the same name you used in the previous step):
    -   `cd my_react_theme/react-src`
    -   `npm run wpstart`
    -   or if you have yarn installed:
    -   `yarn wpstart`
-   That sets up the theme so that it can be seen in the WordPress admin area.
    -   **Go there now and set your WordPress site to use this theme.**
-   View the site in your browser with the new theme.
    -   **You must do this as it does some extra setup via PHP.**
    -   When that's done the theme tells you to `Please restart the Nodejs watcher now...`
    -   To do that, go back to your command prompt where you first ran `npm run wpstart` or `yarn wpstart` and rerun that same command again.
-   In a few seconds you should see your browser load with the standard create-react-app page, but it's running as a WordPress theme!

## Coding Notes

### React Tutorials

If you're looking at a React tutorial on the web, you can use `create-react-wptheme` in place of `create-react-app`.
<br>But you do have to remember that the React app code is one extra folder down inside your theme folder.
<br>Notice that the final folder in this path is `react-src`:

`/xampp/htdocs/wordpress/wp-content/themes/<Your-Create-React-WP-Theme-Folder>/react-src`

So for example, if the tutorial instructs you to edit the `src/App.js` file, then for you, that file would actually be located at (assuming you're already in your new theme's root folder):

`react-src/src/App.js`

### The Public Folder

The authors of the original `create-react-app` say that using the "Public" folder (found at `react-src/public` in your new theme's folder)
is a last ditch "escape hatch" for adding otherwise-hard-to-deal-with files.

But for this project, I've decided to use it for all things that you'd put into a normal WordPress theme (e.g. functions.php, etc.).
So any PHP, hard-coded CSS, and/or hard-coded JS (like other JS libraries that you'd like to reference globally (I don't judge)), can go into
the public folder.

**In addition, any changes made to CSS, JS, and PHP files in the Public folder will cause a rebuild to happen.**
Which is unlike `create-react-app` which ignores most of the files in the Public folder.

I'm not sure if making the Public folder more important is the right decision yet. Maybe I should have created a separate folder for this.
I guess time will tell.

### Dev Configuration

After you've created a new theme, and all the setup is done, you'll find a file named `react-src/user.dev.json` that has some configuration options
that you can mess with if you need to.

### Build Configuration

The `react-src/user.prod.json` configuration file is read when you run `npm run wpbuild` (or `yarn wpbuild`). The only option in there is setting the "homepage"
which controls the relative linking to files in your theme. The "homepage" setting in your main `package.json` file is used during development (and build by default).
During development, this is probably what you want. But if you know your production server will have a different root, then you can set the homepage to be different during
a production build.

For example:

-   Your WordPress dev server is running at http<nolink>://localhost/wordpress
    -   the homepage setting in your main package.json file will probably work just fine.
    -   The homepage line in your main package.json be something like: `"homepage": "/wordpress/wp-content/themes/<your theme's folder name>"`
-   But you know that your production server runs WordPress from the root: http<nolink>://mycoolblog.com/
    -   In this case you want to remove the `/wordpress` part, so set the "homepage" line in your user.prod.json file to:
        `"homepage": "/wp-content/themes/<your theme's folder name>"`
    -   Then run `npm run wpbuild` (or `yarn wpbuild`)
    -   Note that if you then view your theme on your dev server, it will most likely be broken. But will hopefully look
        correct on your production server.

### HTTPS/SSL Support

Obviously, running your WordPress dev server under SSL is no big deal. The theme will just work.

The problem lies in the "Browser Refresh Server." It's a bit of custom code that tells the browser to refresh once Webpack has successfully built
the theme after you save a file. Or in the case of a build not succeeding, it tells the client to render the Error or Warning "overlay."

I haven't figured out how to get that little WebSocket server running with HTTPS/SSL. But, it's only a problem with Firefox,
see here: [Firefox Websocket security issue](https://stackoverflow.com/questions/11768221/firefox-websocket-security-issue).

So if you use Firefox, and you're not willing to change the `about:config` mentioned on that StackOverflow page, then you'll need to switch to Chrome
or some other browser that allows a non-SSL WebSocket to run on a page served over HTTPS.

Any help with getting this little server running with SSL would be much appreciated (the code is here: [react-scripts-wptheme-utils:wpThemeServer.js](https://github.com/devloco/react-scripts-wptheme-utils/blob/master/wpThemeServer.js) -- I'm open to using a completely different library or technology,
as long as we can tell the browser to refresh and print errors, and we don't flood the "Network" tab in a browser's dev tools (e.g. no XHR polling)).

## Goals

-   Remove WebPackDevServer and use your WordPress dev server instead.
    -   Also, do not proxy the WordPress server.
    -   This has many benefits:
        -   One: Never CORS me again.
        -   Two: Never CORS me again.
            -   Just a little _Pacific Rim_ joke for ya.
                -   Just a little _Thor: Ragnarok_ joke for ya.
        -   I mean, really, do you need more reasons than avoiding CORS?
-   No need for any `wp_enqueue_script` stuff.
-   Maintain feature parity(ish) with `create-react-app`
-   Touch the original `react-scripts` as little as possible.
    -   Add new files where possible.
    -   This will make merges easier.
        -   Don't laugh at things like the "return" jammed right in the middle of `init.js`.
            -   [line 113](https://github.com/devloco/react-scripts-wptheme/blob/master/scripts/init.js)
            -   Seriously, I have no idea what the next version of `react-scripts` is going to look like, but I bet that will merge nice-n-easy.
    -   However, something worse than merge conflicts is confusing users.
        -   If completely munging one of the original files will lower the number of support issues... do it!
            -   Example: renaming all the original `create-react-app` scripts by prefixing them with "cra" (e.g. crastart, craeject, etc.)
                will stop users that are used to typing `yarn start` (you should be using `yarn wpstart` instead) and muscle memory-izing
                themselves into confusion.

## Acknowledgements

I'm grateful to the authors of existing related projects for their ideas and collaboration:

-   [create-react-app](https://github.com/facebook/create-react-app)

    The original.

-   [filewatcher-webpack-plugin](https://www.npmjs.com/package/filewatcher-webpack-plugin)

    I used this as an example for writing my own plugin for watching changes to the create-react-app "public" folder.

## License

Create React WP Theme is open source software [licensed as MIT](https://github.com/facebook/create-react-app/blob/master/LICENSE).
