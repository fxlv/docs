To set up your env on windows you need to specify that you're using powershell:
```
docker-machine env --shell powershell dev11
```
or for the complete command:
```
docker-machine.exe env --shell powershell dev11 | Invoke-Expression
```
