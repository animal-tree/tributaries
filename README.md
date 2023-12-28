# Tributaries

A library for mass-deploying [Antelope](https://www.github.com/animal-tree/Antelope) apps on [slurm](https://en.wikipedia.org/wiki/Slurm_Workload_Manager)-enabled remote servers.

```console
pip install tributaries-ml
```

[Examples](Examples)

### Server

Simply create and run a python file with a server configuration like this one:

```python
# MyServer.py

from tributaries import my_server


@my_server(sweep='path/to/my/sweep.py')
def main():
    ...
    return server, username, password, func, app_name_paths, commands, sbatch


if __name__ == '__main__':
    main()
```

That method must return the ```server```, ```username```, and ```password```.

Optionally:
- Any additional ```func``` that needs to be run (e.g. [connecting to a VPN](VPN.py)).
- An ```app_name_paths``` dictionary of names and paths to any Antelope apps' run scripts you'd like to use, *e.g.* ```{'name_of_my_app': 'path/to/name_of_my_app/Run.py'}```, or leave this blank to use the remote server's root home directory and ```ML``` as the run script.
- A ```commands``` list or string of any extra environment-setup commands you may need to pass to the remote server command-line and deploy config such as [activating a conda environment for example](Examples/Servers/XuLab.py#L10).
- Any additional ```sbatch``` string text you'd like to add to the deploy config.

[You may use one of the blueprint server files provided.](Examples/Servers)

### Sweep

Note the Server decorator accepts a ```sweep=``` file path.

You may define a ```sweep``` file like this one:

```python
# path/to/my/sweep.py

from tributaries import my_sweep, my_plots, my_checkpoints

my_sweep.hyperparams = [
    # Hyperparam set 1
    '... experiment=Exp1',

    # Hyperparam set 2
    '... experiment=Exp2'
]

my_sweep.app = 'name_of_my_app'  # Corresponds to an app name in 'app_name_paths' of Server definition

# Logs to download
my_plots.plots = [['Exp1', 'Exp2']]  # Names of experiments to plot together in a single plot

my_checkpoints.experiments = ['Exp1', 'Exp2']  # Names of experiments to download checkpoints for
```

The ```my_sweep``` and ```my_plots``` toggles have [additional configurations](Sweeps.py) that can be used to further customize the launching and plots.

[See here for examples.](Examples/Sweeps) 

### Running

That's it. Running it via ```python MyServer.py``` will launch the corresponding sweep experiments on your remote server. Add the ```plot=true``` flag to instead download plots back down to your local machine.

Add ```checkpoints=true``` to download checkpoints.

#### Launching

```console
python MyServer.py
```

#### Plotting & Logs

```console
python MyServer.py plot=true
```

#### Checkpoints

```console
python MyServer.py checkpoints=true
```

[//]: # (Note: these hyperparams are already fully part of [Antelope]&#40;github.com/animal-tree/Antelope&#41;, together with the ```my_server=``` server-path flag for pointing to a server file, *e.g.*, ```ML my_server=MyServer.main``` can launch and plot the above directly from [Antelope]&#40;github.com/animal-tree/Antelope&#41;! )

### Extra

Note: Tributaries launching fully works for non-Antelope apps too. Also, for convenience, ```tributaries hyperparams='...' app='run.py'``` can be used as a general slurm launcher on your remote servers.

One more thing: if your remote Antelope apps are [git-ssh enabled](https://docs.github.com/en/authentication/connecting-to-github-with-ssh), Tributaries will automatically try syncing with the latest branch via a git pull. You can disable automatic GitHub-syncing with the ```github=false``` flag.

#

[Licensed under the MIT license.](MIT_LICENSE)

<img width="10%" alt="tributaries-logo" src="https://github-production-user-asset-6210df.s3.amazonaws.com/142250284/293290762-0adba97f-d736-4f6a-be45-1c97c0ee1b19.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20231228%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231228T234804Z&X-Amz-Expires=300&X-Amz-Signature=2caec18f4bba001a088aa643cae29e50d31d4869017f25a5bb43de385dc1245a&X-Amz-SignedHeaders=host&actor_id=142250284&key_id=0&repo_id=725474188">
