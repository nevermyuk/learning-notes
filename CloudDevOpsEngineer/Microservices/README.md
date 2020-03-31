## Faas

- Function as a service
- Benefits :
  - No server infrastructure management.
    - Complete abstraction of servers away from the developer.
  - More time focused on writing code, higher developer velocity
  - Functional Programming is a design suited for distributed computing
  - Cost-effective. Billing on demand based on consumption and executions.
  - Services that are event-driven and instantaneously scalable.



## Cloud Computing

| Type         | Benefits                                                     |
| ------------ | ------------------------------------------------------------ |
| Cost         | No upfront cost and resources can be scaled to meet demand   |
| Speed        | Self-service, expert user can leverage resource to build solutions quickly |
| Global scale | Services can be provisioned globally to meet demand in geographic regions. |
| Productivity | Company can focus on building core intellectual property versus reinventing the wheel by building servers. |
| Performance  | Can leverage a continuous upgrade cycle. Network,Storage and compute can improve over time consistently. |
| Reliability  | Redundancy. Multiple regions and data centers for highly available architecture. |
| Security     | Physically access and encryption at rest is assured.         |

## Characteristics of Cloud Native Systems

| Characteristics        | Benefits                                                     |
| ---------------------- | ------------------------------------------------------------ |
| Microservices Oriented | Microservice closely map business logic to code. Can be updated and developed independently. E.g Python AWS Lambda app that use API Gateway |
| Elastic                | Automatically scale to demand.                               |
| Continuous Delivery    | Leverage IaC to define infrastrucuture. Deployment can target a dynamically created environment and software can be automatically installed as it is created. |
| DevOps                 | Combination of automation,processes and tools to increase automation,collaboration and operational efficiency. |
| Agility                | Speed up development and increase quality with IaC and CD.   |
| Composable*            | Highly composable. Each service has API that is consistent and discoverable. Stateless and Modular. |

 Composability: System design principle that deals with the inter-relationships of components. 

## Moore's Law

- Speed and capability of computers can be expected to double every 2 years, as a result of increases in the number of transistor a microchip can contain.

## Pros and Cons of Cloud native Systems

#### Pros:

- Ability to leverage near infinite resources of the cloud: Compute, Disk I/O, Storage, and Memory.
- No up-front costs and resources can be metered to meet demand like an electric or water utility.
- Applications are able to “go global” immediately with no extra investment.
- Increased reliability is increased as many cloud services are themselves highly available. A good example is Amazon S3 which has nine nine’s availability or is 99.999999999% reliable.
- Security is improved by consolidating to a centralized security model where there is a shared security partnership with the cloud vendor. They take care of portions of security such as access to the physical data center.
- The speed applications can be developed and tested are dramatically improved. With concepts like IAC (Infrastructure as Code), complete replicas of a production environment can be provisioned, tested, and then destroyed. This leads to increased quality of software and speed in which software can be developed.

#### Cons:

- Risk of creating systems that rely on a specific cloud vendor.
- The cost involved in migrating an application to a different architecture.
- A current organization may need to hire a new workforce trained to use the cloud or retrain their workforce.



## What is Fault-Tolerance?

**Fault Tolerance** 

- The property that enables a system to continue operating properly in the event of the failure of one or more of its components.
  - E.g a Car, which is designed so it will continue to be drivable if one of the tires is punctured or damaged.
- In computer systems, a fault-tolerant design enables a system to continue its intended operation, possibly at a reduced level, rather than failing completely, when some part of the system fails.

## What is AWS Lambda?

- Function As A Service
- No servers to manage, continuous scaling, and billing at the sub-second level.



### Use cases

---------------

- Triggers with API Gateway. 
  - API Call, URL request will trigger lambda.
- Attach to S3
  - Triggered when new file uploaded into S3
- CloudWatch Event Timer
  - Timer to trigger Lambda every hour.

### Real World Examples

-------------

Company needs to collect competitor's pricing on the same product they are selling.

- Use Lambda function to scrape competitor website
- CloudWatch event timer to run weekly to scrape website and put results into S3
- When S3 receives HTML results, 
  - Another AWS Lambda Function will be triggered to extract pricing information from HTML and write to DynamoDB.
- If value is lower then current value in DB,
  - Website use a third AWS Lambda function
    - Use API Gateway as trigger to serve out companies current price, which will always be the same or lower than competitors price.





### Event and Responses

--------------------------

- Retrieving from event body

  ```python
  body = json.loads(event["body"])
  ```

- Formatting a json response 

  - Have to include key-value pairs.

  ```python
     response = {
          "statusCode": "200",
          "headers": { "Content-type": "application/json" },
          "body": json.dumps({"res": res})
      }
  ```

- Sending a JSON payload as input or test
  - Use POST method.

### IPython

- iPython environment gives a way to program line by line in a terminal window
  - To execute shell commands
  - have to put ! infront of command
    - !pip install ___

## Creating Virtual Env

#### Setting Up and Using Virtualenv

- venv
- Isolates python interpreter to a specific directory

```bash
# Creating a virtual env
python3 -m venv ~/.examplevenv
# To use virtual env, activating by sourcing it
source ~/.examplevenv/bin/activate
```

#### Repeatable convention to Create Virtual Environments

1. Create a virtual env with `python3 -m venv ~/.[reponame]` format
2. Create an alias in Bash by adding into `~/.bashrc`
   - `alias example="cd ~/examplevenv && source ~/.examplevenv/bin/activate"`

3. Each time we open our default shell, alias will be available 

   1. ```bash
      > example
      (examplevenv) user:~/examplevenv $
      ```

      





### Key Terms:

#### Microservice

A lightweight loosely coupled service. It can be as small as a function.

#### FaaS (Function as a Service)

A type of cloud computing that facilitates functions that respond to events.

#### AWS Lambda

A serverless compute platform by AWS that has FaaS capability.

#### Cloud-Native Applications

Cloud-Native applications are services that utilize the unique capabilities of the cloud, like serverless.

#### SQS Queue

A distributed messaging queue built by Amazon with near-infinite reads and writes.

#### Serverless

Serverless is a technique of building applications based on functions and events.

#### Moore's Law

The perception that for some time the number of transistors on a microchip doubles every two years.

#### AWS Cloud9

AWS Cloud9 cloud-based development environment running in AWS. It has special hooks for developing serverless applications.

#### Python Virtual Environment

A python virtual environment is created by isolating a python interpreter to a directory and installing packages in that directory. The python interpreter can perform this action via `python -m venv yournewenv`.