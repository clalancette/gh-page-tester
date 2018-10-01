# Example: Creating an ISO with UDF

This example will show how to create an ISO with the [UDF](standards.md#udf) bridge format.  Here's the complete code for the example:

```
try:
    from cStringIO import StringIO as BytesIO
except ImportError:
    from io import BytesIO

import pycdlib

iso = pycdlib.PyCdlib()
iso.new(udf=True)
foostr = b'foo\n'
iso.add_fp(BytesIO(foostr), len(foostr), '/FOO.;1', udf_path='/foo')
iso.add_directory('/DIR1', udf_path='/dir1')
iso.write('new.iso')
iso.close()
```

Let's take a closer look at the code.

```
try:
    from cStringIO import StringIO as BytesIO
except ImportError:
    from io import BytesIO

import pycdlib
```

As in earlier examples, import the relevant libraries, including pycdlib itself.

```
iso = pycdlib.PyCdlib()
iso.new(udf=True)
```

Create a new PyCdlib object, and then create a new ISO with that object.  In order to make it have UDF, we pass the argument `udf=True` to the [new](pycdlib-api.html#PyCdlib-new) method.

```
foostr = b'foo\n'
iso.add_fp(BytesIO(foostr), len(foostr), '/FOO.;1', udf_path='/foo')
```

As in earlier examples, create a new file on the ISO from a string.  Because this is a UDF ISO, we have to provide the `udf_path` argument to [add_fp](pycdlib-api.html#PyCdlib-add_fp) as well.  Like Joliet, UDF is a completely different namespace from the original ISO9660 structure, and so the argument to be passed here must be an absolute path, not a name.  Because of this, the UDF file can be on a completely different part of the directory structure, or be omitted completely (in which case the file will only show up on the ISO9660 portion of the ISO).

```
iso.add_directory('/DIR1', udf_path='/dir1')
```

Create a new directory on the ISO.  Again we must pass the `udf_path` argument to [add_directory](pycdlib-api.html#PyCdlib-add_directory), for all of the same reasons and with the same restrictions as we saw above for [add_fp](pycdlib-api.html#PyCdlib-add_fp).

```
iso.write('new.iso')
iso.close()
```

Write the new ISO out to a file, then close out the ISO.