Remove all containers
```
docker rm $(docker ps -a -q)
 ```

Delete all images

```
docker rmi $(docker images -q)
```
or if you're brave:
```
docker rmi -f $(docker images -q)
```

