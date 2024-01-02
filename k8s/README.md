# Deployment

## Requirements

What you need:
- Docker installed on your development machine
- A Kubernetes cluster up and running (see [here](../kubemake/README.md))
- Chaos Mesh (see [here](../kubemake/README.md))

## Physical and Digital Brokers

Deploy the physical broker:
```bash
kubectl apply -f physical-broker.yml
```

Deploy the digital broker:
```bash
kubectl apply -f digital-broker.yml
```

## IIoT Device

See [here](../iiot-device/README.md) how to build the Docker image. Then, change the `image: <your-container-registry>` field accordingly in the yaml [file](./iiot-device-1.yml).

Deploy the IIoT device:
```bash
kubectl apply -f iiot-device-1.yml
```

## Digital Twin

See [here](../wldt-digital-twin-mqtt/README.md) how to build the Docker image. Then, change the `image: <your-container-registry>` field accordingly in the yaml [file](./digital-twin-1.yml).

Deploy the digital twin:
```bash
kubectl apply -f digital-twin-1.yml
```

## Experiment Setup

### Purdue Model

| Purdue Model        | Network conditions | Latency ± Jitter            | Loss | File |
|---------------------|--------------------|-----------------------------|------| ---- |
| Layer 0/1           | Regular (R)        | 2.5 ms ± 2.5 ms             | -    | [YAML](./purdue-model/0-0.yml) |
|                     | Deteriorated (D)   | 5 ms ± 5 ms                 | 5 %  | [YAML](./purdue-model/0-1.yml) |
|                     | Critical (C)       | 12.5 ms ± 12.5 ms           | 15 % | [YAML](./purdue-model/0-2.yml) |
| Layer 2             | R                  | 12.5 ms ± 7.5 ms            | -    | [YAML](./purdue-model/1-0.yml) |
|                     | D                  | 25 ms ± 15 ms               | 5 %  | [YAML](./purdue-model/1-1.yml) |
|                     | C                  | 50 ms ± 30 ms               | 15 % | [YAML](./purdue-model/1-2.yml) |
| Layer 3             | R                  | 35 ms ± 15 ms               | -    | [YAML](./purdue-model/2-0.yml) |
|                     | D                  | 70 ms ± 30 ms               | 5 %  | [YAML](./purdue-model/2-1.yml) |
|                     | C                  | 175 ms ± 75 ms              | 15 % | [YAML](./purdue-model/2-2.yml) |

For example, the following command injects the critical network conditions of layer 0/1: 
```bash
kubectl apply -f purdue-model/0-2.yml
```

Delete the injected network effects:
```bash
kubectl delete -f purdue-model/0-2.yml
```

### Illustrative Scenarios

#### Physical Reconfiguration

Reconfigure the IIoT device:
```bash
curl -X PUT -d '{"aggregatedTelemetryMsgSec": 0.5}' \
    <iiot-device-host>:<iiot-device-port>/conf
```

Restore to previous configuration:
```bash
curl -X PUT -d '{"aggregatedTelemetryMsgSec": 1}' \
    <iiot-device-host>:<iiot-device-port>/conf
```

#### Digital Reconfiguration

Reconfigure the digital twin:
```bash
curl -X PUT -d '{"primeNumbersComputationCount": 17500}' \
    <digital-twin-host>:<digital-twin-port>/conf
```

Restore to previous configuration:
```bash
curl -X PUT -d '{"primeNumbersComputationCount": 1000}' \
    <digital-twin-host>:<digital-twin-port>/conf
```

#### Anomaly Detection: Latency

Inject this scenario:
```bash
kubectl apply -f illustrative-scenarios/latency.yml
```

Delete the injected network effects:
```bash
kubectl delete -f illustrative-scenarios/latency.yml
```

#### Anomaly Detection: Latency & Loss

Inject this scenario:
```bash
kubectl apply -f illustrative-scenarios/losslat.yml
```

Delete the injected network effects:
```bash
kubectl delete -f illustrative-scenarios/losslat.yml
```