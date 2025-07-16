https://medium.com/@BuildWithLal/dockerized-gitlab-how-to-easily-set-up-your-own-gitlab-server-9a925be09c59

```
docker pull gitlab/gitlab-ce (Community)
docker pull gitlab/gitlab-ee (Enterproce)

docker run -itd --name git-lab -p 8000:80 gitlab/gitlab-ce
```
```
Browser-> localhost:8000 
          git-lab.example.com:8000
     User: root
     Password: docker exec -it git-lab cat /etc/gitlab/initial_root_password
```
**After login, please reset the default password**
```
     -> User -> Profile -> Password
```
