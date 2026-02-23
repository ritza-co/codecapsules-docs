# Add a Procfile to a Backend Application

A Procfile tells the Backend Capsule which processes to run and how to start them. While Procfiles aren't required on Code Capsules, you can provide one to define custom startup behavior for your application.

Instead of using a Procfile, you can specify build and run commands directly in the Code Capsules UI. For example, the screenshot below shows how to enter a run command for a Node.js application.

![Run command for Node application](../.gitbook/assets/run-command.png)

## Procfile Naming and Location

A Procfile is a plain text file. It must be named exactly `Procfile`, with no extensions (like `.txt` or `.py`) and with an uppercase **P**. The name is case-sensitive, so `procfile` will not work.

The Procfile must be placed in the root directory of your project. It won't work in any other location.

## Procfile Processes

The Procfile defines the processes that a Backend Capsule must run before deploying a backend application. Common process types include `web`, `worker`, and `clock`.

Each process type must be paired with the command that should be used to run it. You can use the command to specify the port the process must run on and other process-specific options.

## Procfile Format

The Procfile uses a key-value format, with each line defining a process type and its corresponding command, as shown below:

```
<process type>: <command>
```

## Example Procfile for Flask

Code Capsules only requires a Procfile for Python applications. Here is an example of how a Procfile for a Flask application might look:

```
web: python3 -m flask run --host=0.0.0.0 --port=$PORT
```

## Procfiles for Other Languages

Express and Java applications don't need a Procfile to be deployed. The Backend Capsule automatically detects the application type and runs the appropriate processes.
