[![powered by ImJoy](https://imjoy.io/static/badge/powered-by-imjoy-badge.svg)](https://imjoy.io/)

# ImJoy Docs: Interactive Documentation Made Easy!
With this tool, you can easily build interactive documentation from markdown files. Readers can try your tool directly in the documentation.

### How it works
We use [docsify](https://docsify.js.org/) to render markdown file within the browser (so no compilation step needed!). For javascript or Python code, if you add an instruction line (i.e.: `<!-- ImJoyPlugin: {} -->`) right before the code block it will be rendered as a executable code block (similar to Jupyter notebooks). When the reader click the Run or Edit button, we will pass the code block into ImJoy and run it as an ImJoy plugin. Depending on the ImJoy plugin type, it will either directly run in the browser or spin up a Jupyter server on MyBinder.org).


### Getting Started
Try the code block below, if you click the **Run** button, you should see a popup message saying `Hello from ImJoy!`.
<!-- ImJoyPlugin: {"type": "web-worker"} -->
```js
api.alert("Hello from ImJoy!")
```
You can also click the **Edit** button, you should see a code editor. Now you can change `Hello from ImJoy!` to `Hello, <YOUR NAME>`, then click the **Run** button in the toolbar of the code editor.

The above code block is rendered as a simplified ImJoy plugin with `type=web-worker` in Javascript:
````
<!-- ImJoyPlugin: {"type": "web-worker"} -->
```js
api.alert("Hello from ImJoy!")
```
````

The full version the above ImJoy plugin is:
````
<!-- ImJoyPlugin: { "type": "web-worker"} -->
```js
class ImJoyPlugin{
    async setup(){
    }
    async run(ctx){
        await api.alert("Hello from ImJoy!")
    }
}
api.export(new ImJoyPlugin())
```
````

You can make a ImJoy plugin in Python (`type=native-python`) and it will run through a Jupyter server on MyBinder.org:

````
<!-- ImJoyPlugin: { "type": "native-python"} -->
```python
from imjoy import api

class ImJoyPlugin():
    async def setup(self):
        pass

    def run(self, ctx):
        api.alert("Hello from a python plugin")

api.export(ImJoyPlugin())
```
````
The above code block will be rendered as:
<!-- ImJoyPlugin: { "type": "native-python"} -->
```python
from imjoy import api

class ImJoyPlugin():
    async def setup(self):
        pass

    def run(self, ctx):
        api.alert("Hello from a python plugin")

api.export(ImJoyPlugin())
```

### Options
In the `ImJoyPlugin` instruction, you can pass additional options. More specifically, they can be any options for the [`<config>` block](https://imjoy.io/docs/#/development?id=ltconfiggt-block) of an ImJoy plugin.

!> If there is no `<config>` block in the code (as in the above examples), you need to at least specify the `type` of the plugin.

!> You will also need to set the correct language for your code block, for Javascript plugin, it has to be `js` or `javascript`, and for Python plugin, it has to be `py` or `python`.

In addition to that, you can also pass the following options:
 * `hide_code_block`: Boolean, optional, if set to `true`, the code will be collapsed by default.
 * `startup_mode`: String. optionsl, either `"run"` means the code block will be executed after loading the page, or `"edit"` means the code editor will be switched on after loading the page.
 * `editor_height`: String. optional, this is the default height for the code editor, it should be a number + `px`, for example: `"500px"`.


### Make interactive docs for your own git repository
It is easy to setup an interactive docs for your own repo. You basically need to copy a few files to your git repo and switch on the static site serving (Github Pages or Gitlab pages etc.). Here are the steps:
 1. In your git repo, create a folder named `docs` (or a different name)
 2. Download this file [index.html](https://raw.githubusercontent.com/imjoy-team/imjoy-docs/master/docs/index.html) and place it under `docs`
 3. Edit the `index.html` file, find the `Configuration` block and change accordingly:
    ```js
        //---------------------Configurations------------------------------
        const GITHUB_USER = 'imjoy-team' // your github user name
        const GITHUB_REPO = 'imjoy-docs' // your github repo name
        const homepage = '/home.md' // the home page markdown file (relative path to the current folder)
        const name = 'ImJoy Docs' // name of this documentation
        const logo = 'https://imjoy.io/static/img/imjoy-logo-black.svg' // a logo show on the upper-left corner
        //--------------------------End------------------------------------
    ```
 4. In `docs` create an empty file named `_sidebar.md`, add your other markdown files into the same folder. Assuming you added a markdown file named `tutorial.md`, you can add it to the side bar by add the following line into `_sidebar.md`:
    ```
     * [Tutorial](/tutorial)
    ```
    In addition, you can also add a home link to the side bar so you will have the following in your `_sidebar.md` file:
    ```
     * [Home](/)
     * [Tutorial](/tutorial)
    ```

    Note: you can also link to external markdown files via their absolute URL (e.g. hosted in another repo), you need be aware that: 1) you need to use the `raw` url 2) the interactive feature won't work on that page.
 5. For Github, you also need to create an empty file named `.nojekyll` (to disable the jekyll site rendering feature of Github)
 6. To see the result locally, you can `cd docs`, start a static server via `python3 -m http.server 8000`, you can then visit `http://localhost:8000` to see your docs.
 7. Commit and push the changes to your Github repo, and go to your git repo settings to switch on Pages service. In Github, you can click `Settings`, scroll down, you can find a section named `GitHub Pages` select your current branch `master` or `main`, then choose the `docs` folder and click `Save`. After some seconds, you can get the URL of your docs. It is recommended to also check `Enforce HTTPS`. 
 
 Optionally, if you have custom domain name, you can also use it, see [here](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/configuring-a-custom-domain-for-your-github-pages-site) for instructions.


?> A complete example can be found here: https://github.com/imjoy-team/imjoy-starter/tree/master/docs

### More examples
This is a more complete show case of all the possibilities: https://imjoy.io/docs/#/i2k_tutorial, and you know more about ImJoy plugins. The pages is rendered from this markdown file [here](https://github.com/imjoy-team/ImJoy/blob/master/docs/i2k_tutorial.md).

We have also other examples that uses ImJoy Docs to do interactive documentation, this includes:
 * [Kaibu API](https://kaibu.org/docs/#/api): [docs source](https://github.com/imjoy-team/kaibu/tree/master/docs)
 * [ImJoy Grid API](https://grid.imjoy.io/docs/#/api): [docs source](https://github.com/imjoy-team/imjoy-grid/tree/master/docs)


As an additional example, you can even embed slides into this docs with ImJoy plugins:
````
<!-- ImJoyPlugin: {"type": "web-worker", "hide_code_block": true, "editor_height": "200px"} -->
```js
api.createWindow({src:"https://gist.github.com/oeway/96cd0f99e87abbcf97d65a3605471130"})
```
````
<!-- ImJoyPlugin: {"type": "web-worker", "hide_code_block": true, "editor_height": "200px"} -->
```js
api.createWindow({src:"https://gist.github.com/oeway/96cd0f99e87abbcf97d65a3605471130"})
```
