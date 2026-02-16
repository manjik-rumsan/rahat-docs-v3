# Service Registration

Each service should register themselves to the hub. Service itself is responsible for regsitaring themselves in the hub. They should provide all the information regarding the services provided by them. We need to flow certain standards for service registartion. 

```json
{
"version":"version",
"uuid":"service_id" "(optional/ if not provided hub willprovide",
"publicAddress":"public wallet address of the service",
"serviceUrl":"gate url to communicate with the service",
"serviceType":"sms",
"paymentToken":"rahat token",
"capabilities":{
"name":"name of the action/service",
"payable": "boolean",
"ratelimit":"number of action service can process",
"perUnit(cpu)":"" ""
}

}
```

During the service registration each service should provide the above details. 

### Field Description

* version: version of the schema
* uuid: unique identifier for the serivce. It's optional. If not provided hub will provide the value during registration.
* publicAddress: public wallet address of the service.
* serviceUrl: main gateway url to communicate with the service.
* serviceType: type of the service provided
* paymentToken: Token name/address used for payment
* capabilities: details of the services provided
   * name: name/tag of the action. example:"send-sms"
   * payable: boolean value,
   * ratelimit: number of action service can process
   * perUnit(cpu): rate per usage of the service.