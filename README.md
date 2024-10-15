# aws-cli-minify
A python script for minifying the aws-cli.

## Requirements
- python3
- `strip` command (from binutils, optional)
- aws-cli zip file

## Usage

1. Download the AWS CLI V2.
[https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html#cliv2-linux-install](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html#cliv2-linux-install)

2. Unzip the AWS CLI.

```
unzip awscliv2.zip
```

3. Create a minify.keep file and add services you *dont* want removed

The file should be a plain text file with each service name on a new line.
The names should match folders in the unzipped AWS folder in `/dist/awscli/boto3/data`

Example:
```
s3
sqs
sns
iam
```

4. Run the minify script passing the path to the unzipped aws dir.

```
$ python aws-cli-minify.py ./aws
```

## What the script does

### Removes unused services
Commands that arn't required are removed from awscli/boto3/data. Each command has its own folder with a number of json files. 
While most of these file are fairly small the large number of services all add up.
We also remove references to these services from endpoints.json.

### Remove examples for unused services
Examples relating to removed services are also removed saving some more space.

### Minify all json files
All whitespace and indentations are remove from json files.

### Strips binaries
As long as the `strip` command is availabe (typically installed from the bintools package in linux) all of the binary files are stripped of debug symbols.

### Removing non-essential files

| File              | Description                                            |
| ----------------- | ------------------------------------------------------ |
| `aws_completer`   | for scripts etc autocomplete isn't needed              |
| `ac.index`        | a sqlite database of commands, CLI works without it.   |
| `libsqlite3.so.0` | sqlite driver, not required since we deleted the db.   |

## Size Reduction

**Default AWS CLI**
Zipped  - 64M
Unzipped - 237M

**Minified AWS CLI** 

With only sqs, sns, s3, iam, rds & ecs enabled

`zip -9 -r aws.min.zip aws`

Zipped - 20M 
Unzipped - 48M

## TODO
- Look at removing other binaries if present in the base image
- Look at while some services (config, deploy opsworkscm) are required
- Add option for automatically unzipping/rezipping the archive
