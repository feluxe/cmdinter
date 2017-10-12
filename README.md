# cmdinter

## Description

This is a library that can be used to apply a *command interface* to functions.
It's main purpose is to apply the *return_code* convention to python functions 
and to give you some control on how to run these functions. E.g. if you need to 
run a function silently (no stdout), if you need to return the stdout that a 
function produces or if you need to prevent a function from throwing errors.

`cmdinter` was created for the [buildlib](https://pypi.python.org/pypi/buildlib) 
package.

## API

### CmdFuncResult

A *cmd function* should return a `CmdFuncResult` object.

```python
class CmdFuncResult(NamedTuple):
    return_code: int
    return_msg: str
    return_val: Any
```

`return_msg`, contains a cmd summary with a Status flag (see Status below).
E.g.:

```
[OK] Run apt-get install.
[Error] pip install package x.
[Skip] Mount hard drive. Drive alrady mounted.
```

`return_code` Unix return_code convention...

`return_val` The value the function returns. That would be what you usually 
return via the `return` keyword.

### Status

Status flags that can be used as `return_msg` prefix.

```python
class Status(object):
    ok: str = '[OK]'
    error: str = '[ERROR]'
    skip: str = '[SKIP]'
```

### run_cmd()

This function can be used in case you want to run a child function as a command.
You run it like this 

```python
result: CmdResult = run_cmd(
    silent=True, 
    return_stdout=True,
)(
    func=my_func,
    args=my_args,
    kwargs=my_kwargs,
)
```

```python
def run_cmd(
    silent: bool = False,
    return_stdout: bool = False,
    catch_err: bool = False,
) -> Callable:
    """
    This function works in combination with functions that return a 
    'CmdFuncResult' object. With `run_cmd()` you get a some more control over
    these functions.
    
    Call it like this:
    
        run_cmd(silent=True, return_stdout=True)(my_func, args, kwargs)
    
    The curried function returns a `CmdResult` object.
    
    @silent: Mute child output of child function if set to True.
    @return_stdout: Return stdout of child function.
    @catch_err: Catch errors that are raised by child functions and return error
                message with 'CmdResult' object.
    """
    # ...
```
 

### CmdResult

A function that is run via the `run_cmd()` function returns a `CmdResult` 
object.

```python
class CmdResult(NamedTuple):
    return_val: Any
    return_code: int
    return_msg: str
    output: Optional[str]
    error: Optional[str]
    traceback: Optional[str]
```

The `CmdResult` object is an extended version of `CmdFuncResult`.

`output` In case you return stdout from a child function, it's stored here.

`error` In case use the `catch_err` option to catch errors from a child function, it's stored here.

`traceback` Error traceback is stored here.


