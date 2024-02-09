# Validating Exposed Ports for the Service in Dockerfile

Ensuring that a container image only exposes the necessary ports for the implemented service is crucial for minimizing the attack surface and following the principle of least privilege. This policy, `validate-ports-used`, aims to validate the exposed ports in a Dockerfile to meet this requirement.

## Policy Details:

- **Policy Name:** validate-ports-used
- **Check Description:** This policy checks whether Dockerfiles expose only the ports used for the service.
- **Policy Category:** Dockerfile Security Best Practices

## How It Works:

### Validation Criteria:

**Key**: Ports

- **Condition:** If `Ports` under EXPOSE contains only specified ports (e.g., 22/tcp, 80/tcp)
  - **Result:** PASS
  - **Reason:** Compliance with the policy is achieved when only the necessary ports for the service are exposed.

- **Condition:** If `Ports` contains any other port
  - **Result:** FAIL
  - **Reason:** Exposing unnecessary ports can increase the attack surface, violating security best practices.

### Policy Validation Testing Instructions

To evaluate and test the policy ensuring Dockerfile compliance:

For testing this policy, follow these steps:
- Ensure you have `kyverno-json` installed on your machine.
- Ensure you have [nctl `v3.4.0`](https://downloads.nirmata.io/nctl/downloads/) or a higher version installed.

1. **Extract JSON equivalent of the Dockerfile:**
    ```bash
    nctl scan dockerfile -r test/good-test/Dockerfile --show-json > payload.json
    ```

2. **Test the Policy with Kyverno:**
    ```bash
    kyverno-json scan --payload payload.json --policy validate-ports-used.yaml
    ```
    
    a. **Test Policy Against Valid Payload:**
    ```bash
    kyverno-json scan --policy validate-ports-used.yaml --payload test/good-test/good-payload.json
    ```

    This produces the output:
    ```
    
    ```

    b. **Test Against Invalid Payload:**
    ```bash
    kyverno-json scan --policy validate-ports-used.yaml --payload test/bad-test/bad-payload.json
    ```

    This produces the output:
    ```
    Loading policies ...
    Loading payload ...
    Pre processing ...
    Running ( evaluating 1 resource against 1 policy ) ...
    - validate-ports-used / validate-ports-used /  FAILED: Exposing unnecessary ports. Reduce the attack surface by only exposing ports used for the service: any[0].check.(Stages[].ExposedPorts | contains("22/tcp") || contains("80/tcp")): Invalid value: false: Expected value: true
    Done
    ```

---

By applying this policy, you enhance the security of your Dockerized applications by restricting the exposed ports to only those necessary for the service, thereby reducing the risk of potential security threats. Adjust the list of allowed ports in the policy as per the specific requirements of your service.