[![powered by ImJoy](https://imjoy.io/static/badge/powered-by-imjoy-badge.svg)](https://imjoy.io/)

# ImJoy Docs: Interactive Documentation Made Easy!
With this tool, you can easily build interactive documentation from markdown files. Readers can try your tool directly in the documentation.

### How it works
We use [docsify](https://docsify.js.org/) to render markdown file within the browser (so no compilation step needed!). To embed Javascript or Python code that can be
executed by the user directly in the browser, you have to add one line (i.e.: `<!-- ImJoyPlugin: {"type": ... } -->`) right before the code block. This code will then be rendered as a executable  block (similar to Jupyter notebooks). When the reader clicks the Run or Edit button, we will pass the code block to ImJoy and run it as an ImJoy plugin. Depending on the ImJoy plugin type, it will either directly run in the browser or spin up a Jupyter server on MyBinder.org).


### See it in action

First, we want to show a simple code block to display `Hello from ImJoy!`
from `type=web-worker` plugin in Javascript. For this, we write in the markdown file:
````
<!-- ImJoyPlugin: {"type": "web-worker"} -->
```js
api.alert("Hello from ImJoy!")
```
````

This will then be rendered as shown below. If you click the **Run** button, you should see a popup message saying `Hello from ImJoy!`.
<!-- ImJoyPlugin: {"type": "web-worker"} -->
```js
api.alert("Hello from ImJoy!")
```

You can also click the **Edit** button, and you should see a code editor. Now you can change `Hello from ImJoy!` to `Hello, <YOUR NAME>`, then click the **Run** button in the toolbar of the code editor.


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

To show a window, for example an image viewer:
````
<!-- ImJoyPlugin: {"type": "web-worker"} -->
```js
api.createWindow({src: "https://kitware.github.io/itk-vtk-viewer/app/"})
```
````
And here is it:
<!-- ImJoyPlugin: {"type": "web-worker"} -->
```js
api.createWindow({src: "https://kitware.github.io/itk-vtk-viewer/app/"})
```

?> You can click the "+" button to make the output window full screen.

You can make a ImJoy plugin in Python (`type=web-python` for Pyodide running in the browser or `type=native-python` for running with remote servers at mybinder.org), which be run through a Jupyter server on MyBinder.org:

````
<!-- ImJoyPlugin: { "type": "web-python"} -->
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
The above code block will be rendered as shown below. Running it will take a few seconds
since the necessary libraries are installed.

<!-- ImJoyPlugin: { "type": "web-python"} -->
```python
from imjoy import api

class ImJoyPlugin():
    async def setup(self):
        pass

    def run(self, ctx):
        api.alert("Hello from a python plugin")

api.export(ImJoyPlugin())
```
### Options for the code block
In the `ImJoyPlugin` instruction, you can pass additional options. More specifically, they can be any options for the [`<config>` block](https://imjoy.io/docs/#/development?id=ltconfiggt-block) of an ImJoy plugin.

!> If there is no `<config>` block in the code (as in the above examples), you need to at least specify the `type` of the plugin.

!> You will also need to set the correct language for your code block, for Javascript plugin, it has to be `js` or `javascript`, and for Python plugin, it has to be `py` or `python`.

In addition to that, you can also pass the following options:
 * `hide_code_block`: Boolean, optional, if set to `true`, the code will be collapsed by default.
 * `startup_mode`: String. optionsl, either `"run"` means the code block will be executed after loading the page, or `"edit"` means the code editor will be switched on after loading the page.
 * `editor_height`: String. optional, this is the default height for the code editor, it should be a number + `px`, for example: `"500px"`.

### Run Python code with Pyodide or Jupyter notebook server

For running Python code, you can choose either `web-python` or `native-python` as plugin type. If you choose `web-python`, then [Pyodide](https://github.com/iodide-project/pyodide) will be used to run Python directly in the browser (no server needed).

Here is an example:
````
<!-- ImJoyPlugin: { "type": "web-python"} -->
```python
from imjoy import api

class ImJoyPlugin():
    async def setup(self):
        pass

    async def run(self, ctx):
        await api.alert("Hello from a python plugin")

api.export(ImJoyPlugin())
```
````
<!-- ImJoyPlugin: { "type": "web-python"} -->
```python
from imjoy import api

class ImJoyPlugin():
    async def setup(self):
        pass

    async def run(self, ctx):
        await api.alert("Hello from a python plugin")

api.export(ImJoyPlugin())
```
?> You can specify python packages via `requirements`(e.g. `{"type": "web-python", "requirements": ["numpy", "pandas"]}`). However, Pyodide only support limited number of libraries, including `numpy`, `scipy`, `pandas`, `scikit-learn`, `pillow` etc.

For accessing more Python libraries, you need to use another type of python which is `native-python`. In order to run it, ImJoy will need to connect to a Jupyter notebook server (JupyterHub or BinderHub). By default, it will automatically connect to MyBinder.org which is a free online Jupyter service. 
For example, if you click the **Run** button below, wait for a while and you will be connected to a Jupyter server on MyBinder.
<!-- ImJoyPlugin: { "type": "native-python"} -->
```python
from imjoy import api

class ImJoyPlugin():
    async def setup(self):
        pass

    async def run(self, ctx):
        await api.alert("Hello from a python plugin")

api.export(ImJoyPlugin())
```
?> MyBinder works by building a Docker image based on a specified Github Repo with specification files (e.g. `requirements.txt` for pip, or `environment.yml` for conda). The default spec is `oeway/imjoy-binder-image/master`, and you can change by pass a URL parameter named `spec` to the ImJoy docs (e.g. `https://imjoy-team.github.io/imjoy-docs/#/?spec=oeway/pyimagej-binder-image/master`)

?> If you want to use your own Jupyter notebook server, you can 1) start your notebook server and obtain the URL with token 2) Pass another URL parameters with your notebook URL as `engine`, for example: `https://imjoy-team.github.io/imjoy-docs/#/?engine=http://127.0.0.1:8080/?token=fad30034546630efk3923l304s3134o20d2592a3f060f3795`.

For most cases, you may want to instruct the user to setup their own Jupyter notebook server and connect to it from the interactive documentation. To do that, we recommended that you suggest them:
 1) Run the `pip install imjoy` command to install the Jupyter notebook and start the notebook by running `imjoy --jupyter`.
 2) Insert the following code block such that the user can get a prompt dialog for pasting their Jupyter server URL:

````
<!-- ImJoyPlugin: { "type": "web-worker", "hide_code_block": true} -->
```js
api.prompt("Please copy and paste your Jupyter notebook URL with token here").then(async (nbUrl)=>{
    if(nbUrl){
        try{
            const engine = await api.getPlugin("Jupyter-Engine-Manager")
            const config = {}
            config.nbUrl = nbUrl
            config.url = config.nbUrl.split("?")[0];
            config.connected = true;
            config.name = "MyCustomNotebook"
            await engine.createEngine(config)
        }
        catch(e){
            await api.alert("Failed to add Jupyter notebook engine, error: " + e.toString())
        }
    }
})
```
````
Try it below:
<!-- ImJoyPlugin: { "type": "web-worker", "hide_code_block": true} -->
```js
api.prompt("Please copy and paste your Jupyter notebook URL with token here").then(async (nbUrl)=>{
    if(nbUrl){
        try{
            const engine = await api.getPlugin("Jupyter-Engine-Manager")
            const config = {}
            config.nbUrl = nbUrl
            config.url = config.nbUrl.split("?")[0];
            config.connected = true;
            config.name = "MyCustomNotebook"
            await engine.createEngine(config)
        }
        catch(e){
            await api.alert("Failed to add Jupyter notebook engine, error: " + e.toString())
        }
    }
})
```
### Create interactive docs for your own git repository
It is easy to setup an interactive docs for your own repo. You basically need to copy a few files to your git repo and switch on the static site serving (Github Pages or Gitlab pages etc.).

?> A complete example can be found here: https://github.com/imjoy-team/imjoy-starter/tree/master/docs

 Here are the steps:
 1. In your Github repository, create a folder named `docs`.
 2. Download the file [index.html](https://raw.githubusercontent.com/imjoy-team/imjoy-docs/master/docs/index.html) and place it in `docs`.
 3. Edit the `index.html` file, find the `Configuration` block and change accordingly:
    ```js
        //---------------------Configurations------------------------------
        const githubUser = 'imjoy-team' // your github user name
        const githubRepo = 'imjoy-docs' // your github repo name
        const homepage = '/home.md' // the home page markdown file (relative path to the current folder)
        const name = 'ImJoy Docs' // name of this documentation
        const logo = 'https://imjoy.io/static/img/imjoy-logo-black.svg' // a logo show on the upper-left corner
        //--------------------------End------------------------------------
    ```
 4. In `docs`, create an empty file named `_sidebar.md`, add your other markdown files into the same folder. Assuming you added a markdown file named `tutorial.md`, you can add it to the side bar by add the following line into `_sidebar.md`:
    ```
     * [Tutorial](/tutorial)
    ```
    In addition, you can also add a home link to the side bar so you will have the following in your `_sidebar.md` file:
    ```
     * [Home](/)
     * [Tutorial](/tutorial)
    ```

    Note: you can also link to external markdown files via their absolute URL (e.g. hosted in another repo), you need be aware that: 1) you need to use the `raw` url 2) the interactive feature won't work on that page.
 5. For Github, you also need to create an empty file named `.nojekyll` (to disable the jekyll site rendering feature of Github).
 6. To **see the result locally**, you need a local Python installation, e.g. with Anaconda. Open a terminal in the `docs` subfolder, and start a static server via `python -m http.server 8000`. You can then visit `http://localhost:8000` to see your docs. If you update your documentation, refresh your site to see the changes. 
 7. Commit and push the changes to your Github repo, and go to your git repo settings to switch on Pages service. In Github, you can click `Settings`, scroll down, you can find a section named `GitHub Pages` select your current branch `master` or `main`, then choose the `docs` folder and click `Save`. After some seconds, you can get the URL of your docs. It is recommended to also check `Enforce HTTPS`.

 Optionally, if you have custom domain name, you can also use it, see [here](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/configuring-a-custom-domain-for-your-github-pages-site) for instructions.

?> An ImJoy core will be loaded to the documentation page, if you want to specify the exact version of imjoy-core, add a new line into the configurations in the `index.html`: `window.ImJoyConfig = {"imjoy_core_version": "latest"}`, you also change `latest` to a specific number(e.g. `0.13.65`) in the line.

### More examples
This is a more complete show case of all the possibilities: https://imjoy.io/docs/#/i2k_tutorial, and you know more about ImJoy plugins. The pages is rendered from this markdown file [here](https://github.com/imjoy-team/ImJoy/blob/master/docs/i2k_tutorial.md).

We have also other examples that uses ImJoy Docs to do interactive documentation, this includes:
 * [Kaibu API](https://kaibu.org/docs/#/api): [docs source](https://github.com/imjoy-team/kaibu/tree/master/docs)
 * [ImJoy Grid API](https://grid.imjoy.io/docs/#/api): [docs source](https://github.com/imjoy-team/imjoy-grid/tree/master/docs)


As an additional example, you can even embed slides into this docs with ImJoy plugins:
````
<!-- ImJoyPlugin: {"type": "web-worker", "hide_code_block": true, "editor_height": "200px"} -->
```js
api.createWindow({src:"https://gist.githubusercontent.com/oeway/96cd0f99e87abbcf97d65a3605471130/raw/95b98eb6a658b397c536d87ec7574219735de9df/SlidesDemo.imjoy.html"})
```
````
<!-- ImJoyPlugin: {"type": "web-worker", "hide_code_block": true, "editor_height": "200px"} -->
```js
api.createWindow({src:"https://gist.githubusercontent.com/oeway/96cd0f99e87abbcf97d65a3605471130/raw/95b98eb6a658b397c536d87ec7574219735de9df/SlidesDemo.imjoy.html"})
```


#### Embed ImJoy App in the docs
You can also embed the ImJoy App as a plugin:
````
<!-- ImJoyPlugin: { "type": "web-worker"} -->
```js
class ImJoyPlugin{
    async setup(){
    }
    async run(ctx){
        const imjoy = await api.createWindow({src: "https://imjoy.io/#/app?workspace=sandbox&flags=quiet"});
        await imjoy.createWindow({src: "https://kitware.github.io/itk-vtk-viewer/app/"})
    }
}
api.export(new ImJoyPlugin())
```
````

Try it below:
<!-- ImJoyPlugin: { "type": "web-worker", "hide_code_block": true} -->
```js
class ImJoyPlugin{
    async setup(){
    }
    async run(ctx){
        // create an imjoy app window
        const imjoy = await api.createWindow({src: "https://imjoy.io/#/app?workspace=sandbox&flags=quiet"});
        // use imjoy.createWindow instead of api.createWindow will make the window appear inside the embedded ImJoy App
        await imjoy.createWindow({src: "https://kitware.github.io/itk-vtk-viewer/app/"})
    }
}
api.export(new ImJoyPlugin())
```

?> We added `workspace=sandbox` and `flags=quiet` to the ImJoy app url (`https://imjoy.io/#/app?workspace=sandbox&flags=quiet`), this can make sure it opens a clean workspace and without the welcome dialog.
