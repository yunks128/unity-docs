# Querying for Resources

### Showing a Summary of Resource by Service Area

1. Generate short-term access keys in Kion, and copy to your clipboard
2. Open a terminal, and paste the keys into your terminal
3. Run the [https://github.com/unity-sds/unity-cs-infra/blob/main/utils/resources-by-service-area.sh](https://github.com/unity-sds/unity-cs-infra/blob/main/utils/resources-by-service-area.sh) program

**Example Run:**

```
% ./resources-by-service-area.sh
----------------------------------
U-CS resources (1 found):
arn:aws:ec2:us-west-2:XXXXXXXX4974:instance/i-0ce1c1ebd2f39XXX0
----------------------------------
U-SPS resources (0 found)
----------------------------------
U-DS resources (0 found)
----------------------------------
U-ADS resources (0 found)
----------------------------------
U-AS resources (0 found)
```
