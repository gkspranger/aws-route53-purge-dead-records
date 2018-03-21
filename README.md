# aws-route53-purge-dead-records
Purge AWS R53 dead records for a known node name pattern using Ansible

```bash
ansible-playbook -i <dynamic-inventory-of-all-ec2s> aws_r53_purge_dead_records.yml -e "aws_profile=<aws-local-profile>"
```

!! BEWARE !! Please review the code and make sure you know what is going on .. I tried to keep it simple, but the scope of how/where this works is limited: Here are some known things to keep an eye out for:

* the Ansible inventory needs to contain ALL running EC2s
  * dynamic inventory makes the most sense
* That way you can compare what is in R53 against what is known and current and running
* Uses AWS profile to talk to AWS API
* There are many pauses along the way to make sure you can stop whenever you want
* in the purge.yml file, there is a "filter" you can apply to make sure you are only going to validate a predictable node name pattern

```python
  - set_fact:
      asg_records: "{{ asg_records + [ item.Name | regex_replace ('\\.$', '') ] }}"
    when: |
      item.Type == "A" and
      item.Name | search("asg-")
    with_items: "{{ r53_records.ResourceRecordSets }}"

```
