# registrar

**PORT:** 4096

The registrar keeps track of known nodes and their respective sensors. Interfacing with the registar **should** take place via the `crate` library as it will ensure submissions and retrievals conform to the correct node formatting.

**Note:** The registrar is not a particular binary. It is an instance of `rekdb` running with the database set to `/tmp/registrar.db` and the port being `4096`.