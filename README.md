# udacity-project-1
# Create the Policy Definition (Subscription scope)
az policy definition create --name tagging-policy  --rules Policy/tagging_policy.rules.json --param Policy/tagging_policy.params.json

# Create the Policy Assignment
# Set the scope to a resource group; may also be a subscription or management group
az policy assignment create --name tagging-policy --policy tagging-policy --param Policy/tagging-policy.assignment.json