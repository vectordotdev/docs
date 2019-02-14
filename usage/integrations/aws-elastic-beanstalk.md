---
description: Send AWS Elastic Bean Stalk logs to Timber
---

# AWS Elastic Bean Stalk

Timber integrates with [AWS Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/) through it's [ebextensions feature](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html), enabling you to forward log files to to Timber.

## Installation

1. In your `shell` add the `TIMBER_API_KEY` environment variable:  


   ```bash
   eb setenv TIMBER_API_KEY={{my-timber-api-key}}
   ```

   \(you can also do this in the [AWS EB console](https://console.aws.amazon.com/elasticbeanstalk) itself by following [these instructions](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environments-cfg-softwaresettings.html#environments-cfg-softwaresettings-console).\)

2. Add the [`01_timber.config` file](https://gist.github.com/binarylogic/26f97f4ef3589bdbdd14e65fd4c002a8) to your `.ebextensions` folder.
3. Package your source bundle as usual and upload it to AWS Elastic Beanstalk to deploy a new version

