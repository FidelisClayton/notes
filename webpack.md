### Webpack not watching changes?
Just increase the number of watchers (ubuntu):
```sh
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```
