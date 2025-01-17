conda
=====

Installing conda
----------------

To install conda, we use `Mambaforge
<https://github.com/conda-forge/miniforge>`_. Mambaforge is a way of installing
conda that sets conda-forge as the default (and only) channel, and it includes
``mamba``, a drop-in replacement for conda that is faster and more
feature-complete.

conda activate, and tmux
------------------------
After installing conda, you would typically run ``conda init bash``. This adds lines
to your ``.bashrc`` file (if on Linux) or your ``.bash_profile`` file (if on
Mac). Thereafter, every new bash session will run that code, which involves
importing some Python libraries and running conda itself. The conda Python
module loads other modules, and those modules load still others, so that many
files are touched.

This is totally fine when on a personal machine, since it's unlikely that
you'll be starting so many bash sessions that you notice any sort of slowdown
from the I/O load of many files. But this kind of I/O slowdown could easily
happen in a high-performance computing environment (like NIH's Biowulf) when
many jobs are kicked off simultaneously. So we actually want to avoid this on
HPC environments.

In addition, using conda within tmux can get frustrating (on either a personal
computer or HPC system). You may find that on tmux, when you create a new pane
or window, your prompt still indicates that you're in a conda environment, but
running ``which python`` returns the *system* Python. It turns out that this is
because tmux prepends the system path to the PATH when creating a new pane or
window. The solution is to run ``conda deactivate`` a bunch of times, until the
prompt no longer has the ``(env_name)`` indicator in it, and *then* activate the
environment you want.

The solution
------------

.. note::

    Use ``ca`` (short for "conda activate") to activate an environment
    at the time you want to use it.

    ``ca`` by itself to activate the base environment

    ``ca <envname>`` to activate a specific environment

    Every new tmux pane starts with everything deactivated.

These dotfiles provide a function, ``ca`` (short for "conda activate"). It's
defined in the ``~/.functions`` file which in turn is sourced in ``~/.bashrc``.
This function runs ``eval "$(conda shell.bash hook)"``, which is a way of
running the same commands that ``conda init bash`` would add to your
``~/.bashrc``. But it runs it on demand, not every shell.

The ``~/.bashrc`` also has a ``conda_deactivate_all`` function, which keeps
running ``conda deactivate`` until there's no activated environment (this idea
is from `this SO answer <https://stackoverflow.com/a/76304995>`_).

Last, theres a line in ``~/.bashrc`` that runs ``conda_deactivate_all`` if we're
in a tmux session, so it will run on each new tmux pane.
