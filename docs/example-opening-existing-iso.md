# Example: Opening an existing ISO

This example will show how to examine an existing ISO.  Here's the complete code for this example:

```
import sys
import pycdlib

iso = pycdlib.PyCdlib()
iso.open(sys.argv[1])

for child in iso.list_children(iso_path='/'):
    print(child.file_identifier())

iso.close()
```

Let's take a closer look at the code.

```
import sys
import pycdlib
```

As we've seen before, import pycdlib.  We also import the sys module so we get access to the command-line arguments.

```
iso = pycdlib.PyCdlib()
iso.open(sys.argv[1])
```

As we saw in the last example, create a new PyCdlib object.  Once we have the object, we can then open up the file passed on the command-line.  During the open, PyCdlib will parse all of the metadata on the ISO, so if the file is coming over a network, this may take a bit of time.  Note that besides the [open](pycdlib-api.html#PyCdlib-open) method, there is also an [open_fp](pycdlib-api.html#PyCdlib-open_fp) method that takes an arbitrary file-like object.

```
for child in iso.list_children(iso_path='/'):
    print(child.file_identifier())
```

Use the [list_children](pycdlib-api.html#PyCdlib-list_children) API from PyCdlib to iterate over all of the files and directories at the root of the ISO.  As discussed in the [Creating a new, basic ISO](example-creating-new-basic-iso.md) example, the paths are Unix-like absolute paths.

```
iso.close()
```

Close out the PyCdlib object, releasing all resources and invalidating the contents.  After this call, the object can be reused to create a new ISO or open up an existing ISO.
