# Kustomize transformers

## Common transformers

- commonLabel – adds a label to all Kubernetes resources

- namePrefix/Suffix – adds a common prefix-suffix to all resource
names

- Namespace – adds a common namespace to all resources 
  
- commonAnnotations – adds an annotation to all resources

## Image transformaers

- #### `newName` - change a specific name of an image to the new name 
```
 images:
  - name: nginx
    newName: haproxy
 
```

- #### `newTag` - change the tag of an image 
```
 images:
  - name: nginx
    newTag: 2.4
 
```
 