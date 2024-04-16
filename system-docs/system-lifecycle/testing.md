# Testing

Software development requires different levels of testing at each individual level fo the development lifecycle. Thought the scope of the testing is different, it **shall always be automated where possible**.

### Rules

1. All software is tested
2. All tests are automated
3. Only 'released' code can go to the Test environment
4. Only tested releases in the **Test** environment can go to Production

### Unity Development Environments

| Environment                         | Owner          | Description                                                                                            | Gate                   | Tenant Accessible |
| ----------------------------------- | -------------- | ------------------------------------------------------------------------------------------------------ | ---------------------- | ----------------- |
| Development                         | Dev Teams      | Environment for the initial and continued development of a unity software component                    | Unit test              | No                |
| Software Integration and Test (SIT) | Dev team leads | Environment for the integration between components of the Unity system (across service areas)          | Unit Test              |                   |
| Test                                | PSE/I\&T       | Environment for mature components and formal testing of the entire system before release to production | Integration tests      | No                |
| Production                          | PSE            | Production environment for the Unity platform                                                          | System Release Testing | Yes               |

### Test Plan and Procedures

* Service teams own all unit and 'integration' testing within their service area
* unit and integration tests shall all be automated and run on checkin **and/or** deployment
* PSE will own the test plan for the TEST environment
* Development Teams/Service areas own the test procedures/executions for the TEST envrionment
* Test plan will focus on system level testing exercising the standards based interfaces, not the underlying implementation, that is, it should test the WPS, not ‘HySDS’.

These 2 items ensure that a certain level of validation is occurring at the system level while not blocking development teams. This work has to be _**synergistic**_.

The test plan will be derived from Epics and requirements, and will focus mainly on 'functional' requirements.

Test procedures should be automated wherever possible in order to minimize the time from release to tested to operational deployment. The goal should be to remove any person or role from those steps where possible.
