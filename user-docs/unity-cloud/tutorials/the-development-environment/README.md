# The Development Environment

The Unity algorithm development environment is based on the Jupyter notebook environment.&#x20;

### Jupyter? Notebooks? Huh?

If you're confused by any of these words, and even if you're somewhat familiar with them, it is recommended to take a look at the [jupyter documentation.](https://docs.jupyter.org/en/latest/) Specifically, understanding the [notebook interface](https://jupyter-notebook.readthedocs.io/en/latest/) and [notebook examples](https://jupyter-notebook.readthedocs.io/en/latest/examples/Notebook/examples\_index.html)&#x20;

### Unity Development Environment At a glance

#### Instance Types

<figure><img src="../../../../.gitbook/assets/Screenshot 2024-04-16 at 7.50.20 AM.png" alt="" width="518"><figcaption><p>Environments available once you log in</p></figcaption></figure>

When you log in to jupyter for the first time, you'll have a single option- start my server- available to you. After clicking that button, you'll be given the option of several environments to use. As a project, you can request specific environments with specific libraries included by default. The ones you see above are the general environments supplied to all Unity users. There are no difference between them with regards to CPU and Memory resources. For specifics on hardward specifications, please reach out to your venue administrator.

#### Unity Interface



<figure><img src="../../../../.gitbook/assets/Screenshot 2024-04-16 at 7.57.35 AM.png" alt="" width="563"><figcaption><p>The Jupyter Interface</p></figcaption></figure>

Once you log in and select an instance type, you'll be presented the general interface above. For specifics on using the environment, please check out the [#jupyter-notebooks-huh](./#jupyter-notebooks-huh "mention") section above.

The left hand side is your file explorer, where you can upload, download, and navigate the files within your notebook instance. **note:** This is not a "shared" environment, files placed here are not available to other notebook users, and they are not made available "in the cloud". These are only available to you when you're logged into the notebook. These files are persisted between logins, so you can go away and come back in a month and all your files should be here.

On the right hand side, is the launcher- this allows you to quickly start a notebook (to write code and algorithms) or launch a console (a terminal) in the notebook environment.

The console is where you can use package managers like pip and conda to install more packages in your environment that are not a part of your default environment. Pacakge managers are also critical in [developing an algorithm](users-guide.md)- the next tutorial.
