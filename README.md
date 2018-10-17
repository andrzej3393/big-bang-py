## TL;DR Checklist

- Use [.gitignore](https://git-scm.com/docs/gitignore).

- Use [Python 3.7](https://docs.python.org/3/whatsnew/3.7.html).

- Manage dependencies using [Pipenv](https://pipenv.readthedocs.io/en/latest).

- Manage and execute command line tasks via [Invoke](http://www.pyinvoke.org).

- Format Python files with [YAPF](https://github.com/google/yapf).

- Sort Python imports with [Isort](https://github.com/timothycrosley/isort).

- Scan code complexity by calling `run-mccabe.py` script.


## .gitignore

- ALWAYS use `.gitignore`. It specifies files intentionally ignored by Git.

- If you are using PyCharm, definitely install [.ignore plugin](https://github.com/hsz/idea-gitignore). This will make managing `.gitignore` a breeze. 

- Alternatively, it is possible to generate .gitignore online using [gitignore.io](https://www.gitignore.io).

- You may also find [example](https://bitbucket.org/rtbhouse/big-bang-py/src/master/.gitignore) of Python project .gitignore in this repo.


## Python version

- **It is recommended to use Python 3.7.**

- There is a nice summary of new features on [Real Python](https://realpython.com/python37-new-features). More detailed description can be found in [official docs](https://docs.python.org/3/whatsnew/3.7.html).

- To name some notable additions:

    - [Data Classes](https://realpython.com/python-data-classes) can easily replace [namedtuples](https://docs.python.org/3.7/library/collections.html#collections.namedtuple) and [attrs](https://github.com/python-attrs/attrs).
    
    - [Typing Forward Reference](https://realpython.com/python37-new-features/#typing-enhancements) makes type hints even more programmer-friendly.

    - The `asyncio` module has received many [new features, usability and performance improvements](https://docs.python.org/3/whatsnew/3.7.html#asyncio). For instance,  new `asyncio.run()` function can be used to run a coroutine from synchronous code by automatically creating and destroying the event loop.
    
- If for some reason you are still using previous versions of Python (especially version 3.6), it is possible to backport some features e.g. [Data Classes](https://github.com/ericvsmith/dataclasses).


## Pipenv - Python Packing Tool

- **Use [Pipenv](https://pipenv.readthedocs.io/en/latest) as Python packaging tool.**

- Pipenv consolidates `pip` & `virtualenv` as well as offers some powerful features:

    - Pipenv automatically creates and manages a virtualenv.
    
    - You specify the project packages in `Pipfile`. Because those are abstract dependency declarations, **you declare only the packages you need**.
    
    -  `Pipfile.lock` holds locked/hashed combination of all dependencies (including sub-dependencies) of your project. This ensures repeatable, deterministic builds. You never manage this file by hand, as Pipenv takes care of it.
    
    - Essential commands:
    
        - **`shell`: spawns a shell with the virtualenv activated. If a `.env` file is present in your project, shell will automatically load it for you.**
        
        - `run`: runs a given command from the virtualenv, with any arguments forwarded (e.g. `$ pipenv run python`).
        
        - `graph`: shows a dependency graph of installed dependencies.
        
        - `check`: **checks for security vulnerabilities** and asserts that PEP 508 requirements are being met by the current environment.

- You can educate yourself further by reading a [Real Python's guide](https://realpython.com/pipenv-guide). It is also recommended to go through [the official documentation](https://pipenv.readthedocs.io/en/latest/).


## Invoke - Task Management & Command Execution

- It is recommended to **turn into a task every project related shell command** which will be called more than a couple of times and is not super-common (like `ls` with basic flags).

- **Manage and execute those project tasks via [Invoke](http://www.pyinvoke.org).**

- You can easily replace `Makefiles` and similar tools as Invoke is dead simple.

- Invoke tasks are called by typing in the shell `invoke *task-name*`

- Invoke tasks are normal Python functions organised in `tasks.py` file.

- Docstrings of Invoke tasks functions are neatly converted into classic command line tool help:

```
>>> invoke --list
Available tasks:

  task1      First line of docstring of task1.
  task2      First line of docstring of task2.

# Get full docstring of a particular task:  
>>> invoke --help *task-name*
```

- [Invoke tasks can be organised using namespaces](http://docs.pyinvoke.org/en/1.2/getting-started.html#creating-namespaces). Then, for instance, you can call server tasks like `jenkins.deploy`/`jenkins.logs` or organise job-related tasks like `job.start`/`job.stop`.

- You may find examples of tasks in this repo's [tasks.py](https://bitbucket.org/rtbhouse/big-bang-py/src/master/tasks.py).

- [The official documentation](http://docs.pyinvoke.org/en/1.2/) is solid, so it is recommended get familiar with it. 


## YAPF - Python Files Formatter

- **The ultimate goal of [YAPF](https://github.com/google/yapf) is to produce code as good as the code that a programmer would write if they were following a style guide.**

- The formatting style used by YAPF is configurable. Specific configuration can be pointed in a couple of ways. However, it is recommended to simply store it in a properly formatted `.style.yapf` file in the root of your project, so YAPF can automatically pick this config up.

- You may find pre-configured [.style.yapf](https://bitbucket.org/rtbhouse/big-bang-py/src/master/.style.yapf) in the root of this repo.

    - Invoke task `update_yapf` in [tasks.py](https://bitbucket.org/rtbhouse/big-bang-py/src/master/tasks.py) updates your project's `.style.yapf` by copying the version from the local clone of repo Big-Bang-py (https://bitbucket.org/rtbhouse/big-bang-py).

- It is recommended to include YAPF in your linting Invoke task and also run it during pre-commit Git Hook. Example of both can be found in this repo, respectively in [tasks.py](https://bitbucket.org/rtbhouse/big-bang-py/src/master/tasks.py) and [pre-commit](https://bitbucket.org/rtbhouse/big-bang-py/src/master/pre-commit) files.

- Survival tips:
    
    - If you leave trailing comma in a collection (list, tuple, function parameters etc.) YAPF will force the collection to break giving one element per line.
    
    - From time to time you WILL see weirdly formatted code as YAPF is not perfect. There are at least two major occurrences: 
    
        - Deeply Nested Dicts - This is quite understandable as decisions that improve readability are usually arbitrary and should be solved on a case-by-case basis.
         
        - Complex Comprehensions - YAPF assumes that only comprehension that exceed column limit are complex enough to be formatted... This issue is brought to the attention of both YAPF authors and to the general public as well (see posts on [Github](https://github.com/google/yapf/issues/628), [Stack Overflow](https://stackoverflow.com/questions/52558919/is-there-a-way-to-force-yapf-to-always-split-fold-comprehensions) and [Reddit](https://www.reddit.com/r/Python/comments/9mov4r/is_there_a_way_to_force_yapf_to_always_splitfold)).
  
    - To manage all edge cases, [disable YAPF per line or per block](https://github.com/google/yapf#why-does-yapf-destroy-my-awesome-formatting). 
    

## Isort - Python Imports Sorter

- [Isort](https://github.com/timothycrosley/isort) is a Python library to sort imports.

- Most importantly, Isort sorts imports alphabetically and separates them into defined sections. 

- There are several ways to configure Isort's behavior. Full reference of every setting can be found [here](https://github.com/timothycrosley/isort/wiki/isort-Settings#full-reference-of-isort-settings).

- You can specify project level configuration simply by placing a `.isort.cfg` file at the root of your project.

- You may find pre-configured [.isort.cfg](https://bitbucket.org/rtbhouse/big-bang-py/src/master/.isort.cfg) in the root of this repo.

    - Invoke task `update_isort` in [tasks.py](https://bitbucket.org/rtbhouse/big-bang-py/src/master/tasks.py) updates your project's `.isort.cfg` by copying the version from the local clone of repo Big-Bang-py (https://bitbucket.org/rtbhouse/big-bang-py).
    
- It is recommended to include Isort in your linting Invoke task and also run it during pre-commit Git Hook. Example of both can be found in this repo, respectively in [tasks.py](https://bitbucket.org/rtbhouse/big-bang-py/src/master/tasks.py) and [pre-commit](https://bitbucket.org/rtbhouse/big-bang-py/src/master/pre-commit) files.

- To manage all edge cases, [disable Isort per line or for entire file](https://github.com/timothycrosley/isort#skip-processing-of-imports-outside-of-configuration).


## McCabe - Code Complexity Checker

- [mccabe](https://github.com/pycqa/mccabe) library automatically detects over-complex code based on cyclomatic complexity (for curious, consult [tutorialspoint](https://www.tutorialspoint.com/software_testing_dictionary/cyclomatic_complexity.htm) and [Wikipedia](https://en.wikipedia.org/wiki/Cyclomatic_complexity)).

- Cyclomatic complexity is roughly equivalent to one plus the number of loops and if statements. The simple interpretation is that it is an upper bound for the number of test cases required to obtain branch coverage of the code. So, in the context of testing, cyclomatic complexity can be used to estimate the required effort for writing tests.

- Code with high cyclomatic complexity (usually assumed as 10+) is likely to be difficult to understand and therefore have a higher probability of containing defects.
    
- It is recommended to include McCabe in your linting Invoke task and also run it during pre-commit Git Hook. Example of both can be found in this repo, respectively in [tasks.py](https://bitbucket.org/rtbhouse/big-bang-py/src/master/tasks.py) and [pre-commit](https://bitbucket.org/rtbhouse/big-bang-py/src/master/pre-commit) files.

    - Cut-off complexity is currently assumed to be 7. However, this number should be adjusted to reflect your experience and project's needs.
    
- Invoke task `update_mccabe` in [tasks.py](https://bitbucket.org/rtbhouse/big-bang-py/src/master/tasks.py) updates your project's `run-mccabe.py` script by copying the version from the local clone of repo Big-Bang-py (https://bitbucket.org/rtbhouse/big-bang-py).
