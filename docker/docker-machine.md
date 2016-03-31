Create docker machine 

```
docker-machine create --driver virtualbox dev1
```

To set up your env on windows you need to specify that you're using powershell:
```
docker-machine env --shell powershell dev1
```
or for the complete command:
```
docker-machine env --shell powershell dev1 | Invoke-Expression
```
