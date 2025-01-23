* PostgreSQL’s different major versions are incompatible, while different minor versions are com-
patible. What does such incompatibility mean? PostgreSQL stores data in binary format, and
this format could possibly change between major versions. This means that, while you are able
to upgrade PostgreSQL between minor versions on the fly, you probably will have to dump and
restore your database content between major version upgrades.

* PostgreSQL is a service, which means it runs as a daemon on the operating system; a running
PostgreSQL daemon is called an instance. A PostgreSQL instance is often called a cluster because
a single instance can serve and handle multiple databases. Every database is an isolated space
where users and applications can store data..

* Database objects are represented by everything the user can create and manage within the da-
tabase—for instance, tables, functions, triggers, and data types. Every object belongs to one and
only one schema that, if not specified, is named as the user that creates the object

* PostgreSQL allows the configuration of as many superusers as you need, and every superuser
has the very same permissions: they can do everything with every database and object and, most
notably, can also control the life cycle of the cluster (for instance, they can terminate normal user
connections, reload the configuration, stop the whole cluster, and so on).

* PostgreSQL internal data, such as users, databases, namespaces, configuration, and database run-
time status, is provided by means of catalogs: special tables and views that present information
in a SQL-interactive way. Many catalogs are trimmed depending on the user who is inspecting
them, with the exception that superusers usually see the whole set of available information

* This is an important point to keep in mind: PostgreSQL relies on the underlying filesystem to
implement persistence, and therefore tuning the filesystem is an important task in order to make
PostgreSQL perform well. In particular, PostgreSQL stores all of its content (user data and in-
ternal status) in a single filesystem directory known as PGDATA. The PGDATA directory represents
what the cluster is serving as databases, so it is possible for you to have a single installation of
PostgreSQL and make it switch to different PGDATA directories to deliver different content. As you
will see in the next sections, the PGDATA directory needs to be initialized before it can be used by
PostgreSQL; the initialization is the creation of the directory structure within PGDATA itself and
is, of course, a one-time operation.

* In short, PGDATA directory is where PostgreSQL expects to find data and
configuration files. In particular, the PGDATA directory is made up of at least the Write-Ahead
Logs (WALs) and the data storage. Without either of those two parts, the cluster is unable to
guarantee data consistency and, in some critical circumstances, even start.

* WALs are a technology that many database systems use, and the basic idea of how they work is
shared with other technologies like transactional filesystems (such as ZFS, UFS with Soft Updates,
and so on). The idea is that, before applying any change to a chunk of data, an intent log will
be made persistent. In this case, if the cluster crashes, it can always rely on the already-written
intent log to understand what operations have been completed and what must be recovered.

* Internally, PostgreSQL keeps track of the tables’ structures, indexes, functions, and all the stuff
needed to manage the cluster in its dedicated storage, the catalog.

* The SQL standard defines a so-called information schema, a collection of tables common to all standard 
database implementations, including PostgreSQL, that the DBA can use to inspect the internal status of 
the database itself. For instance, the in-formation schema defines a table that collects information 
about all the user-defined tables so that it is possible to query the information schema to see whether a 
specific table exists or not. The PostgreSQL catalog is what could be called an “information schema on 
steroids”: the catalog is much more accurate and PostgreSQL-specific than the general information schema, 
and the DBA can extract a lot more information about the PostgreSQL status from the catalog. Of course,
PostgreSQL does support the information schema, but throughout the whole book, you will see references to 
the catalogs because they provide much more detailed information.

* When the cluster is started, PostgreSQL launches a single process called the postmaster. The aim of
the postmaster is to bootstrap the instance, spawning needed processes to manage the database
activity, and then to wait for incoming connections. A user connection, often made over a TCP/
IP connection, requires the postmaster to fork another process named the backend process, which
in turn is in charge of serving one and only one connection.

    This means that every time a new connection against the cluster is opened, the cluster reacts by
    launching a new backend process to serve it until the connection ends and the process is, conse-
    quently, destroyed. The postmaster usually also starts some utility processes that are responsible
    for keeping PostgreSQL in good shape while it is running