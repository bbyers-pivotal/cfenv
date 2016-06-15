# CFEnv

Example sinatra app which exposes the [Cloud Foundry](http://cloudfoundry.org) instance index when the app is deployed to Cloud Foundry.

## 1) Setup Prerequisites
#### Install Virtualbox
* https://www.virtualbox.org/wiki/Downloads

#### Install Go
* https://golang.org/dl/

#### Install PCF Dev
* __Demo should be ran on a Mac__
* Download PCF Dev
  * https://network.pivotal.io/products/pcfdev
* Install PCF Dev as CF plugin
  * ```$ cf install-plugin path/to/pcfdevfile```
  * It will prompt for API key, which can be found at https://network.pivotal.io/users/dashboard/edit-profile
* After PCF Dev is installed, run ```$ cf dev start``` and wait for the VM to start up

#### Install CF Autopilot Plugin
```
$ go get github.com/concourse/autopilot
$ cf install-plugin $GOPATH/bin/autopilot
```

#### Create Redis Service
```
$ cf create-service p-redis shared-vm myredis
```

#### Install watch
```
$ brew install watch
```


## 2) Initial Deployment

```
$ cf push
```


## 3) Show how the application scales

### Setup monitoring of deployed app
* Open new terminal windows for the following commands
 * ```watch -n 1 cf app pivotal-cfenv```
 * ```watch -n 1 cf routes```
 * ```watch -n 1 cf services```
 * ```while true; do sleep 1; curl http://pivotal-cfenv.local.pcfdev.io; echo ''; done```

### Scale App Horizontally
Observe the output from the command windows. Notice how the number of app instances changes
```
$ cf scale pivotal-cfenv -i 5
```

### Showing blue/green deployment between branches
With the existing monitoring windows still running...
* ```$ git checkout enterprise-features```
* ```$ cf zero-downtime-push pivotal-cfenv -p . -f manifest.yml```

Observe the output from the command windows and notice how the new app is added, routes/services bound, then once up and running, old app is removed

## Customization

Modify `manifest.yml` to change the running characteristics of the app in Cloud Foundry.
