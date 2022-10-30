# SNMP_sandbox
A sandbox environment for testing custom SNMP profiles with Datadog Network Device Management. By adding your own Datadog API key to an `.env` file and then running a simple run command, you can validate your custom SNMP profile with the help of a mock SNMP device running in another Docker container.

##### Use Case
To validate custom SNMP profiles along with Datadog agent configuration.

## How to Use

Simply clone the repo, add your custom SNMP profile to test along with `conf.yaml` file to test it against.

* Custom profile to be tested must be named `_test_profile.yaml` and configuration file named `conf.yaml`
* Both `_test_profile.yaml` and `conf.yaml` must be placed in the `/snmp` directory of this repo for this program to run properly
* In order to collect metrics, there must be `OID`s matching what is broadcasted in the `.snmprec` file for the Datadog agent to successfully poll the mock SNMP device.
* The Datadog agent will only collect metrics that are defined in a profile, if the `OID` isn't in the `.snmprec` file, it won't be collected by Datadog

This script requires a `.env` file to be placed in the repository's parent directory (./). Your `.env` file should contain environmental variables needed for your docker containers, in this case your `DD_API_KEY`.

If you don't already have a `.env` file, you can create one by running the command below in the `snmp` directory :
```
touch .env
```
You can then add the necessary environment variables as depicted below:

`DD_API_KEY= <YOUR_API_KEY_HERE>`

##### File Placement

![run sh — SNMP_sandbox 2022-10-30 at 12 30 50 PM](https://user-images.githubusercontent.com/49233513/198890249-6d105987-0359-4bd3-b4e4-e751781ed8aa.jpg)


To run this script, simply run the follwing script from this repository's parent directory(`SNMP_sandbox`).
```
./run.sh
```
The run script simply builds fresh Docker images at runtime, leveraging the docker-compose command.

##### To stop the containers and destroy the Docker network and images created runt he below command:
```
./destroy.sh
```

# A Live Demonstration
* Commands used:
  * `git clone git@github.com:UTXOnly/SNMP_sandbox.git`
  * `cp <Filepath_to_conf.yaml>/conf.yaml ./conf.yaml`
  * `cp <Filepath_to_test_profile.yaml>/conf.yaml ./_test_profile.yaml`

[Gif of run.sh script](https://a.cl.ly/xQux8w5P)
![Gif of run scipt](https://user-images.githubusercontent.com/49233513/198861452-dc78d1c9-1b9b-49b0-9943-3e4a11a14d59.gif)


[In the Datadog NDM panel](https://a.cl.ly/GGuzwKje)
![In the Datadog NDM panel](https://user-images.githubusercontent.com/49233513/198861534-cd973b7e-c0a2-4d33-9fec-2fd5c30351d8.gif)




## Configuration Notes

`conf.yaml` should match what is shown in [example config file](https://github.com/DataDog/integrations-core/blob/master/snmp/datadog_checks/snmp/data/conf.yaml.example)

* The program will automatically correct the value corresponding to the `ip_address:` key to `host.docker.internal`. The program aslo loads the `_test_profile.yaml` configuration file to the `/etc/datadog-agent/conf.d/snmp.d/profiles` directory.
* Filename for custom profile to test must be `_test_profile.yaml` for the program to work properly


```
init_config:
  loader: core
  use_device_id_as_hostname: true
  profiles:
    custom-profile:
      definition_file: _test_profile.yaml
      
instances:

- ip_address: 192.168.1.123
  snmp_version: 2
  profile: custom-profile
  loader: core
  community_string: 'mockSNMP'
  use_device_id_as_hostname: true
  ```
  


##### Community String Usage for SNMP v2
* The key for `community_string` needs to match the name of the `.snmprec` file that is used to mimic the networking device, be sure to enclose in single quotes `'`



Just run the program and the data will start showing up in the Datadog UI.


## To connect to the running Datadog container

```
docker exec -it datadog-agent /bin/bash
```

## To Do
* Add functionality for SNMP `v3` 
  

