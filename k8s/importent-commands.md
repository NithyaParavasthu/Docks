# Important Commands to Know

To find the latest supported versions of kubernetes APIs use the following command
```
for kind in `kubectl api-resources | tail +2 | awk '{ print $1 }'`; do kubectl explain $kind; done | grep -e "KIND:" -e "VERSION:"
```

