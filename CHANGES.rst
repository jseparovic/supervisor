4.3.0.dev0 (Next Release)
-------------------------

- Fixed a bug where the XML-RPC method ``supervisor.startProcess()`` would
  return 500 Internal Server Error instead of an XML-RPC fault response
  if the command could not be parsed.  Patch by Julien Le Cléach.

- Fixed a bug on Python 2.7 where a ``UnicodeDecodeError`` may have occurred
  when using the web interface.  Patch by Vinay Sajip.

- Removed use of ``urllib.parse`` functions ``splithost``, ``splitport``, and
  ``splittype`` deprecated in Python 3.8.

- Removed use of ``asynchat`` and ``asyncore`` deprecated in Python 3.10.

- ``supervisorctl`` now reads extra files included via the ``[include]``
  section in ``supervisord.conf`` like ``supervisord`` does.  This allows
  the ``[supervisorctl]`` section or ``[ctlplugin:x]`` sections to be in
  included files.  Patch by François Granade.

- The return value of the XML-RPC method ``supervisor.getAllConfigInfo()``
  now includes the ``directory``, ``uid``, and ``serverurl`` of the
  program.  Patch by Yellmean.

- If ``supervisord`` searches the default paths for its config file (no
  ``-c`` flag given), it will now print a message showing the path of the
  config file that it loaded.  Patch by Alexander Tuna.

- If a subprocess exits with a unexpected exit code (one not listed in
  ``exitcodes=`` in a ``[program:x]`` section) then the exit will now be logged
  at the ``WARN`` level instead of ``INFO``.  Patch by Precy Lee.

- ``supervisorctl shutdown`` now shows an error message if an argument is
  given.

4.2.4 (2021-12-30)
------------------

- Fixed a bug where the ``--identifier`` command line argument was ignored.
  It was broken since at least 3.0a7 (released in 2009) and probably earlier.
  Patch by Julien Le Cléach.

4.2.3 (2021-12-27)
------------------

- Fixed a race condition where an ``rpcinterface`` extension that subscribed
  to events would not see the correct process state if it accessed the
  the ``state`` attribute on a ``Subprocess`` instance immediately in the
  event callback.  Patch by Chao Wang.

- Added the ``setuptools`` package to the list of dependencies in
  ``setup.py`` because it is a runtime dependency.  Patch by Louis Sautier.

- The web interface will now return a 404 Not Found response if a log file
  is missing.  Previously, it would return 410 Gone.  It was changed because
  410 is intended to mean that the condition is likely to be permanent.  A
  log file missing is usually temporary, e.g. a process that was never started
  will not have a log file but will have one as soon as it is started.

4.2.2 (2021-02-26)
------------------

- Fixed a bug where ``supervisord`` could crash if a subprocess exited
  immediately before trying to kill it.

- Fixed a bug where the ``stdout_syslog`` and ``stderr_syslog`` options
  of a ``[program:x]`` section could not be used unless file logging for
  the same program had also been configured.  The file and syslog options
  can now be used independently.  Patch by Scott Stroupe.

- Fixed a bug where the ``logfile`` option in the ``[supervisord]``
  section would not log to syslog when the special filename of
  ``syslog`` was supplied, as is supported by all other log filename
  options.  Patch by Franck Cuny.

- Fixed a bug where environment variables defined in ``environment=``
  in the ``[supervisord]`` section or a ``[program:x]`` section could
  not be used in ``%(ENV_x)s`` expansions.  Patch by MythRen.

- The  ``supervisorctl signal`` command now allows a signal to be sent
  when a process is in the ``STOPPING`` state.  Patch by Mike Gould.

- ``supervisorctl`` and ``supervisord`` now print help when given ``-?``
  in addition to the existing ``-h``/``--help``.

4.2.1 (2020-08-20)
------------------

- Fixed a bug on Python 3 where a network error could cause ``supervisord``
  to crash with the error ``<class 'TypeError'>:can't concat str to bytes``.
  Patch by Vinay Sajip.

- Fixed a bug where a test would fail on systems with glibc 2.3.1 because
  the default value of SOMAXCONN changed.

4.2.0 (2020-04-30)
------------------

- When ``supervisord`` is run in the foreground, a new ``--silent`` option
  suppresses the main log from being echoed to ``stdout`` as it normally
  would.  Patch by Trevor Foster.

- Parsing ``command=`` now supports a new expansion, ``%(numprocs)d``, that
  expands to the value of ``numprocs=`` in the same section.  Patch by
  Santjago Corkez.

- Web UI buttons no longer use background images.  Patch by Dmytro Karpovych.

- The Web UI now has a link to view ``tail -f stderr`` for a process in
  addition to the existing ``tail -f stdout`` link.  Based on a
  patch by OuroborosCoding.

- The HTTP server will now send an ``X-Accel-Buffering: no`` header in
  logtail responses to fix Nginx proxy buffering.  Patch by Weizhao Li.

- When ``supervisord`` reaps an unknown PID, it will now log a description
  of the ``waitpid`` status.  Patch by Andrey Zelenchuk.

- Fixed a bug introduced in 4.0.3 where ``supervisorctl tail -f foo | grep bar``
  would fail with the error ``NoneType object has no attribute 'lower'``.  This
  only occurred on Python 2.7 and only when piped.  Patch by Slawa Pidgorny.

4.1.0 (2019-10-19)
------------------

- Fixed a bug on Python 3 only where logging to syslog did not work and
  would log the exception ``TypeError: a bytes-like object is required, not 'str'``
  to the main ``supervisord`` log file.  Patch by Vinay Sajip and Josh Staley.

- Fixed a Python 3.8 compatibility issue caused by the removal of
  ``cgi.escape()``.  Patch by Mattia Procopio.

- The ``meld3`` package is no longer a dependency.  A version of ``meld3``
  is now included within the ``supervisor`` package itself.

4.0.4 (2019-07-15)
------------------

- Fixed a bug where ``supervisorctl tail <name> stdout`` would actually tail
  ``stderr``.  Note that ``tail <name>`` without the explicit ``stdout``
  correctly tailed ``stdout``.  The bug existed since 3.0a3 (released in
  2007).  Patch by Arseny Hofman.

- Improved the warning message added in 4.0.3 so it is now emitted for
  both ``tail`` and ``tail -f``.  Patch by Vinay Sajip.

- CVE-2019-12105.  Documentation addition only, no code changes.  This CVE
  states that ``inet_http_server`` does not use authentication by default
  (`details <https://github.com/Supervisor/supervisor/issues/1245>`_).  Note that
  ``inet_http_server`` is not enabled by default, and is also not enabled
  in the example configuration output by ``echo_supervisord_conf``.  The
  behavior of the ``inet_http_server`` options have been correctly documented,
  and have not changed, since the feature was introduced in 2006.  A new
  `warning message <https://github.com/Supervisor/supervisor/commit/4e334d9cf2a1daff685893e35e72398437df3dcb>`_
  was added to the documentation.

4.0.3 (2019-05-22)
------------------

- Fixed an issue on Python 2 where running ``supervisorctl tail -f <name>``
  would fail with the message
  ``Cannot connect, error: <type 'exceptions.UnicodeEncodeError'>`` where it
  may have worked on Supervisor 3.x.  The issue was introduced in Supervisor
  4.0.0 due to new bytes/strings conversions necessary to add Python 3 support.
  For ``supervisorctl`` to correctly display logs with Unicode characters, the
  terminal encoding specified by the environment must support it.  If not, the
  ``UnicodeEncodeError`` may still occur on either Python 2 or 3.  A new
  warning message is now printed if a problematic terminal encoding is
  detected.  Patch by Vinay Sajip.

4.0.2 (2019-04-17)
------------------

- Fixed a bug where inline comments in the config file were not parsed
  correctly such that the comments were included as part of the values.
  This only occurred on Python 2, and only where the environment had an
  extra ``configparser`` module installed.  The bug was introduced in
  Supervisor 4.0.0 because of Python 2/3 compatibility code that expected
  a Python 2 environment to only have a ``ConfigParser`` module.

4.0.1 (2019-04-10)
------------------

- Fixed an issue on Python 3 where an ``OSError: [Errno 29] Illegal seek``
  would occur if ``logfile`` in the ``[supervisord]`` section was set to
  a special file like ``/dev/stdout`` that was not seekable, even if
  ``logfile_maxbytes = 0`` was set to disable rotation.  The issue only
  affected the main log and not child logs.  Patch by Martin Falatic.

4.0.0 (2019-04-05)
------------------

- Support for Python 3 has been added.  On Python 3, Supervisor requires
  Python 3.4 or later.  Many thanks to Vinay Sajip, Scott Maxwell, Palm Kevin,
  Tres Seaver, Marc Abramowitz, Son Nguyen, Shane Hathaway, Evan Andrews,
  and Ethan Hann who all made major contributions to the Python 3 porting
  effort.  Thanks also to all contributors who submitted issue reports and
  patches towards this effort.

- Support for Python 2.4, 2.5, and 2.6 has been dropped.  On Python 2,
  Supervisor now requires Python 2.7.

- The ``supervisor`` package is no longer a namespace package.

- The behavior of the config file expansion ``%(here)s`` has changed.  In
  previous versions, a bug caused ``%(here)s`` to always expand to the
  directory of the root config file.  Now, when ``%(here)s`` is used inside
  a file included via ``[include]``, it will expand to the directory of
  that file.  Thanks to Alex Eftimie and Zoltan Toth-Czifra for the patches.

- The default value for the config file setting ``exitcodes=``, the expected
  exit codes of a program, has changed.  In previous versions, it was ``0,2``.
  This caused issues with Golang programs where ``panic()`` causes the exit
  code to be ``2``.  The default value for ``exitcodes`` is now ``0``.

- An undocumented feature where multiple ``supervisorctl`` commands could be
  combined on a single line separated by semicolons has been removed.

- ``supervisorctl`` will now set its exit code to a non-zero value when an
  error condition occurs.  Previous versions did not set the exit code for
  most error conditions so it was almost always 0.  Patch by Luke Weber.

- Added new ``stdout_syslog`` and ``stderr_syslog`` options to the config
  file.  These are boolean options that indicate whether process output will
  be sent to syslog.  Supervisor can now log to both files and syslog at the
  same time.  Specifying a log filename of ``syslog`` is still supported
  but deprecated.  Patch by Jason R. Coombs.

3.4.0 (2019-04-05)
------------------

- FastCGI programs (``[fcgi-program:x]`` sections) can now be used in
  groups (``[group:x]``).  Patch by Florian Apolloner.

- Added a new ``socket_backlog`` option to the ``[fcgi-program:x]`` section
  to set the listen(2) socket backlog.  Patch by Nenad Merdanovic.

- Fixed a bug where ``SupervisorTransport`` (the XML-RPC transport used with
  Unix domain sockets) did not close the connection when ``close()`` was
  called on it.  Patch by Jérome Perrin.

- Fixed a bug where ``supervisorctl start <name>`` could hang for a long time
  if the system clock rolled back.  Patch by Joe LeVeque.

3.3.5 (2018-12-22)
------------------

- Fixed a race condition where ``supervisord`` would cancel a shutdown
  already in progress if it received ``SIGHUP``.  Now, ``supervisord`` will
  ignore ``SIGHUP`` if shutdown is already in progress.  Patch by Livanh.

- Fixed a bug where searching for a relative command ignored changes to
  ``PATH`` made in ``environment=``.  Based on a patch by dongweiming.

- ``childutils.ProcessCommunicationsProtocol`` now does an explicit
  ``flush()`` after writing to ``stdout``.

- A more descriptive error message is now emitted if a name in the config
  file contains a disallowed character.  Patch by Rick van Hattem.

3.3.4 (2018-02-15)
------------------

- Fixed a bug where rereading the configuration would not detect changes to
  eventlisteners.  Patch by Michael Ihde.

- Fixed a bug where the warning ``Supervisord is running as root and it is
  searching for its config file`` may have been incorrectly shown by
  ``supervisorctl`` if its executable name was changed.

- Fixed a bug where ``supervisord`` would continue starting up if the
  ``[supervisord]`` section of the config file specified ``user=`` but
  ``setuid()`` to that user failed.  It will now exit immediately if it
  cannot drop privileges.

- Fixed a bug in the web interface where redirect URLs did not have a slash
  between the host and query string, which caused issues when proxying with
  Nginx.  Patch by Luke Weber.

- When ``supervisord`` successfully drops privileges during startup, it is now
  logged at the ``INFO`` level instead of ``CRIT``.

- The HTTP server now returns a Content-Type header specifying UTF-8 encoding.
  This may fix display issues in some browsers.  Patch by Katenkka.

3.3.3 (2017-07-24)
------------------

- Fixed CVE-2017-11610.  A vulnerability was found where an authenticated
  client can send a malicious XML-RPC request to ``supervisord`` that will
  run arbitrary shell commands on the server.  The commands will be run as
  the same user as ``supervisord``.  Depending on how ``supervisord`` has been
  configured, this may be root.  See
  https://github.com/Supervisor/supervisor/issues/964 for details.

3.3.2 (2017-06-03)
------------------

- Fixed a bug introduced in 3.3.0 where the ``supervisorctl reload`` command
  would crash ``supervisord`` with the error ``OSError: [Errno 9] Bad file
  descriptor`` if the ``kqueue`` poller was used.  Patch by Jared Suttles.

- Fixed a bug introduced in 3.3.0 where ``supervisord`` could get stuck in a
  polling loop after the web interface was used, causing high CPU usage.
  Patch by Jared Suttles.

- Fixed a bug where if ``supervisord`` attempted to start but aborted due to
  another running instance of ``supervisord`` with the same config, the
  pidfile of the running instance would be deleted.  Patch by coldnight.

- Fixed a bug where ``supervisorctl fg`` would swallow most XML-RPC faults.
  ``fg`` now prints the fault and exits.

- Parsing the config file will now fail with an error message if a process
  or group name contains a forward slash character (``/``) since it would
  break the URLs used by the web interface.

- ``supervisorctl reload`` now shows an error message if an argument is
  given.  Patch by Joel Krauska.

- ``supervisorctl`` commands ``avail``, ``reread``, and ``version`` now show
  an error message if an argument is given.

3.3.1 (2016-08-02)
------------------

- Fixed an issue where ``supervisord`` could hang when responding to HTTP
  requests (including ``supervisorctl`` commands) if the system time was set
  back after ``supervisord`` was started.

- Zope ``trackrefs``, a debugging tool that was included in the ``tests``
  directory but hadn't been used for years, has been removed.

3.3.0 (2016-05-14)
------------------

- ``supervisord`` will now use ``kqueue``, ``poll``, or ``select`` to monitor
  its file descriptors, in that order, depending on what is available on the
  system.  Previous versions used ``select`` only and would crash with the error
  ``ValueError: filedescriptor out of range in select()`` when running a large
  number of subprocesses (whatever number resulted in enough file descriptors
  to exceed the fixed-size file descriptor table used by ``select``, which is
  typically 1024).  Patch by Igor Sobreira.

- ``/etc/supervisor/supervisord.conf`` has been added to the config file search
  paths.  Many versions of Supervisor packaged for Debian and Ubuntu have
  included a patch that added this path.  This difference was reported in a
  number of tickets as a source of confusion and upgrade difficulties, so the
  path has been added.  Patch by Kelvin Wong.

- Glob patterns in the ``[include]`` section now support the
  ``host_node_name`` expansion.  Patch by Paul Lockaby.

- Files included via the ``[include]`` section are now logged at the ``INFO``
  level instead of ``WARN``.  Patch by Daniel Hahler.

3.2.4 (2017-07-24)
------------------

- Backported from Supervisor 3.3.3:  Fixed CVE-2017-11610.  A vulnerability
  was found where an authenticated client can send a malicious XML-RPC request
  to ``supervisord`` that will run arbitrary shell commands on the server.
  The commands will be run as the same user as ``supervisord``.  Depending on
  how ``supervisord`` has been configured, this may be root.  See
  https://github.com/Supervisor/supervisor/issues/964 for details.

3.2.3 (2016-03-19)
------------------

- 400 Bad Request is now returned if an XML-RPC request is received with
  invalid body data.  In previous versions, 500 Internal Server Error
  was returned.

3.2.2 (2016-03-04)
------------------

- Parsing the config file will now fail with an error message if an
  ``inet_http_server`` or ``unix_http_server`` section contains a ``username=``
  but no ``password=``.  In previous versions, ``supervisord`` would start with
  this invalid configuration but the HTTP server would always return a 500
  Internal Server Error.  Thanks to Chris Ergatides for reporting this issue.

3.2.1 (2016-02-06)
------------------

- Fixed a server exception ``OverflowError: int exceeds XML-RPC limits`` that
  made ``supervisorctl status`` unusable if the system time was far into the
  future.  The XML-RPC API returns timestamps as XML-RPC integers, but
  timestamps will exceed the maximum value of an XML-RPC integer in January
  2038 ("Year 2038 Problem").  For now, timestamps exceeding the maximum
  integer will be capped at the maximum to avoid the exception and retain
  compatibility with existing API clients.  In a future version of the API,
  the return type for timestamps will be changed.

3.2.0 (2015-11-30)
------------------

- Files included via the ``[include]`` section are read in sorted order.  In
  past versions, the order was undefined.  Patch by Ionel Cristian Mărieș.

- ``supervisorctl start`` and ``supervisorctl stop`` now complete more quickly
  when handling many processes.  Thanks to Chris McDonough for this patch.
  See: https://github.com/Supervisor/supervisor/issues/131

- Environment variables are now expanded for all config file options.
  Patch by Dexter Tad-y.

- Added ``signalProcess``, ``signalProcessGroup``, and ``signalAllProcesses``
  XML-RPC methods to supervisor RPC interface.  Thanks to Casey Callendrello,
  Marc Abramowitz, and Moriyoshi Koizumi for the patches.

- Added ``signal`` command to supervisorctl.  Thanks to Moriyoshi Koizumi and
  Marc Abramowitz for the patches.

- Errors caused by bad values in a config file now show the config section
  to make debugging easier.  Patch by Marc Abramowitz.

- Setting ``redirect_stderr=true`` in an ``[eventlistener:x]`` section is now
  disallowed because any messages written to ``stderr`` would interfere
  with the eventlistener protocol on ``stdout``.

- Fixed a bug where spawning a process could cause ``supervisord`` to crash
  if an ``IOError`` occurred while setting up logging.  One way this could
  happen is if a log filename was accidentally set to a directory instead
  of a file.  Thanks to Grzegorz Nosek for reporting this issue.

- Fixed a bug introduced in 3.1.0 where ``supervisord`` could crash when
  attempting to display a resource limit error.

- Fixed a bug where ``supervisord`` could crash with the message
  ``Assertion failed for processname: RUNNING not in STARTING`` if a time
  change caused the last start time of the process to be in the future.
  Thanks to Róbert Nagy, Sergey Leschenko, and samhair for the patches.

- A warning is now logged if an eventlistener enters the UNKNOWN state,
  which usually indicates a bug in the eventlistener.  Thanks to Steve
  Winton and detailyang for reporting issues that led to this change.

- Errors from the web interface are now logged at the ``ERROR`` level.
  Previously, they were logged at the ``TRACE`` level and easily
  missed.  Thanks to Thomas Güttler for reporting this issue.

- Fixed ``DeprecationWarning: Parameters to load are deprecated. Call
  .resolve and .require separately.`` on setuptools >= 11.3.

- If ``redirect_stderr=true`` and ``stderr_logfile=auto``, no stderr log
  file will be created.  In previous versions, an empty stderr log file
  would be created.  Thanks to Łukasz Kożuchowski for the initial patch.

- Fixed an issue in Medusa that would cause ``supervisorctl tail -f`` to
  disconnect if many other ``supervisorctl`` commands were run in parallel.
  Patch by Stefan Friesel.

3.1.4 (2017-07-24)
------------------

- Backported from Supervisor 3.3.3:  Fixed CVE-2017-11610.  A vulnerability
  was found where an authenticated client can send a malicious XML-RPC request
  to ``supervisord`` that will run arbitrary shell commands on the server.
  The commands will be run as the same user as ``supervisord``.  Depending on
  how ``supervisord`` has been configured, this may be root.  See
  https://github.com/Supervisor/supervisor/issues/964 for details.

3.1.3 (2014-10-28)
------------------

- Fixed an XML-RPC bug where the ElementTree-based parser handled strings
  like ``<value><string>hello</string></value>`` but not strings like
  ``<value>hello</value>``, which are valid in the XML-RPC spec.  This
  fixes compatibility with the Apache XML-RPC client for Java and
  possibly other clients.

3.1.2 (2014-09-07)
------------------

- Fixed a bug where ``tail group:*`` in ``supervisorctl`` would show a 500
  Internal Server Error rather than a BAD_NAME fault.

- Fixed a bug where the web interface would show a 500 Internal Server Error
  instead of an error message for some process start faults.

- Removed medusa files not used by Supervisor.

3.1.1 (2014-08-11)
------------------

- Fixed a bug where ``supervisorctl tail -f name`` output would stop if log
  rotation occurred while tailing.

- Prevent a crash when a greater number of file descriptors were attempted to
  be opened than permitted by the environment when starting a bunch of
  programs.  Now, instead a spawn error is logged.

- Compute "channel delay" properly, fixing symptoms where a supervisorctl
  start command would hang for a very long time when a process (or many
  processes) are spewing to their stdout or stderr.  See comments attached to
  https://github.com/Supervisor/supervisor/pull/263 .

- Added ``docs/conf.py``, ``docs/Makefile``, and ``supervisor/scripts/*.py``
  to the release package.

3.1.0 (2014-07-29)
------------------

- The output of the ``start``, ``stop``, ``restart``, and ``clear`` commands
  in ``supervisorctl`` has been changed to be consistent with the ``status``
  command.  Previously, the ``status`` command would show a process like
  ``foo:foo_01`` but starting that process would show ``foo_01: started``
  (note the group prefix ``foo:`` was missing).  Now, starting the process
  will show ``foo:foo_01: started``.  Suggested by Chris Wood.

- The ``status`` command in ``supervisorctl`` now supports group name
  syntax: ``status group:*``.

- The process column in the table output by the ``status`` command in
  ``supervisorctl`` now expands to fit the widest name.

- The ``update`` command in ``supervisorctl`` now accepts optional group
  names.  When group names are specified, only those groups will be
  updated.  Patch by Gary M. Josack.

- Tab completion in ``supervisorctl`` has been improved and now works for
  more cases.  Thanks to Mathieu Longtin and Marc Abramowitz for the patches.

- Attempting to start or stop a process group in ``supervisorctl`` with the
  ``group:*`` syntax will now show the same error message as the ``process``
  syntax if the name does not exist.  Previously, it would show a Python
  exception.  Patch by George Ang.

- Added new ``PROCESS_GROUP_ADDED`` and ``PROCESS_GROUP_REMOVED`` events.
  These events are fired when process groups are added or removed from
  Supervisor's runtime configuration when using the ``add`` and ``remove``
  commands in ``supervisorctl``.  Patch by Brent Tubbs.

- Stopping a process in the backoff state now changes it to the stopped
  state.  Previously, an attempt to stop a process in backoff would be
  ignored.  Patch by Pascal Varet.

- The ``directory`` option is now expanded separately for each process in
  a homogeneous process group.  This allows each process to have its own
  working directory.  Patch by Perttu Ranta-aho.

- Removed ``setuptools`` from the ``requires`` list in ``setup.py`` because
  it caused installation issues on some systems.

- Fixed a bug in Medusa where the HTTP Basic authorizer would cause an
  exception if the password contained a colon.  Thanks to Thomas Güttler
  for reporting this issue.

- Fixed an XML-RPC bug where calling supervisor.clearProcessLogs() with a
  name like ``group:*`` would cause a 500 Internal Server Error rather than
  returning a BAD_NAME fault.

- Fixed a hang that could occur in ``supervisord`` if log rotation is used
  and an outside program deletes an active log file.  Patch by Magnus Lycka.

- A warning is now logged if a glob pattern in an ``[include]`` section does
  not match any files.  Patch by Daniel Hahler.

3.0.1 (2017-07-24)
------------------

- Backported from Supervisor 3.3.3:  Fixed CVE-2017-11610.  A vulnerability
  was found where an authenticated client can send a malicious XML-RPC request
  to ``supervisord`` that will run arbitrary shell commands on the server.
  The commands will be run as the same user as ``supervisord``.  Depending on
  how ``supervisord`` has been configured, this may be root.  See
  https://github.com/Supervisor/supervisor/issues/964 for details.

3.0 (2013-07-30)
----------------

- Parsing the config file will now fail with an error message if a process
  or group name contains characters that are not compatible with the
  eventlistener protocol.

- Fixed a bug where the ``tail -f`` command in ``supervisorctl`` would fail
  if the combined length of the username and password was over 56 characters.

- Reading the config file now gives a separate error message when the config
  file exists but can't be read.  Previously, any error reading the file
  would be reported as "could not find config file".  Patch by Jens Rantil.

- Fixed an XML-RPC bug where array elements after the first would be ignored
  when using the ElementTree-based XML parser.  Patch by Zev Benjamin.

- Fixed the usage message output by ``supervisorctl`` to show the correct
  default config file path.  Patch by Alek Storm.

3.0b2 (2013-05-28)
------------------

- The behavior of the program option ``user`` has changed.  In all previous
  versions, if ``supervisord`` failed to switch to the user, a warning would
  be sent to the stderr log but the child process would still be spawned.
  This means that a mistake in the config file could result in a child
  process being unintentionally spawned as root.  Now, ``supervisord`` will
  not spawn the child unless it was able to successfully switch to the user.
  Thanks to Igor Partola for reporting this issue.

- If a user specified in the config file does not exist on the system,
  ``supervisord`` will now print an error and refuse to start.

- Reverted a change to logging introduced in 3.0b1 that was intended to allow
  multiple processes to log to the same file with the rotating log handler.
  The implementation caused supervisord to crash during reload and to leak
  file handles.  Also, since log rotation options are given on a per-program
  basis, impossible configurations could be created (conflicting rotation
  options for the same file).  Given this and that supervisord now has syslog
  support, it was decided to remove this feature.  A warning was added to the
  documentation that two processes may not log to the same file.

- Fixed a bug where parsing ``command=`` could cause supervisord to crash if
  shlex.split() fails, such as a bad quoting.  Patch by Scott Wilson.

- It is now possible to use ``supervisorctl`` on a machine with no
  ``supervisord.conf`` file by supplying the connection information in
  command line options.  Patch by Jens Rantil.

- Fixed a bug where supervisord would crash if the syslog handler was used
  and supervisord received SIGUSR2 (log reopen request).

- Fixed an XML-RPC bug where calling supervisor.getProcessInfo() with a bad
  name would cause a 500 Internal Server Error rather than the returning
  a BAD_NAME fault.

- Added a favicon to the web interface.  Patch by Caio Ariede.

- Fixed a test failure due to incorrect handling of daylight savings time
  in the childutils tests.  Patch by Ildar Hizbulin.

- Fixed a number of pyflakes warnings for unused variables, imports, and
  dead code.  Patch by Philippe Ombredanne.

3.0b1 (2012-09-10)
------------------

- Fixed a bug where parsing ``environment=`` did not verify that key/value
  pairs were correctly separated.  Patch by Martijn Pieters.

- Fixed a bug in the HTTP server code that could cause unnecessary delays
  when sending large responses.  Patch by Philip Zeyliger.

- When supervisord starts up as root, if the ``-c`` flag was not provided, a
  warning is now emitted to the console.  Rationale: supervisord looks in the
  current working directory for a ``supervisord.conf`` file; someone might
  trick the root user into starting supervisord while cd'ed into a directory
  that has a rogue ``supervisord.conf``.

- A warning was added to the documentation about the security implications of
  starting supervisord without the ``-c`` flag.

- Add a boolean program option ``stopasgroup``, defaulting to false.
  When true, the flag causes supervisor to send the stop signal to the
  whole process group.  This is useful for programs, such as Flask in debug
  mode, that do not propagate stop signals to their children, leaving them
  orphaned.

- Python 2.3 is no longer supported.  The last version that supported Python
  2.3 is Supervisor 3.0a12.

- Removed the unused "supervisor_rpc" entry point from setup.py.

- Fixed a bug in the rotating log handler that would cause unexpected
  results when two processes were set to log to the same file.  Patch
  by Whit Morriss.

- Fixed a bug in config file reloading where each reload could leak memory
  because a list of warning messages would be appended but never cleared.
  Patch by Philip Zeyliger.

- Added a new Syslog log handler.  Thanks to Denis Bilenko, Nathan L. Smith,
  and Jason R. Coombs, who each contributed to the patch.

- Put all change history into a single file (CHANGES.txt).

3.0a12 (2011-12-06)
-------------------

- Released to replace a broken 3.0a11 package where non-Python files were
  not included in the package.

3.0a11 (2011-12-06)
-------------------

- Added a new file, ``PLUGINS.rst``, with a listing of third-party plugins
  for Supervisor.  Contributed by Jens Rantil.

- The ``pid`` command in supervisorctl can now be used to retrieve the PIDs
  of child processes.  See ``help pid``.  Patch by Gregory Wisniewski.

- Added a new ``host_node_name`` expansion that will be expanded to the
  value returned by Python's ``platform.node`` (see
  http://docs.python.org/library/platform.html#platform.node).
  Patch by Joseph Kondel.

- Fixed a bug in the web interface where pages over 64K would be truncated.
  Thanks to Drew Perttula and Timothy Jones for reporting this.

- Renamed ``README.txt`` to ``README.rst`` so GitHub renders the file as
  ReStructuredText.

- The XML-RPC server is now compatible with clients that do not send empty
  <params> when there are no parameters for the method call.  Thanks to
  Johannes Becker for reporting this.

- Fixed ``supervisorctl --help`` output to show the correct program name.

- The behavior of the configuration options ``minfds`` and ``minprocs`` has
  changed.  Previously, if a hard limit was less than ``minfds`` or
  ``minprocs``, supervisord would unconditionally abort with an error.  Now,
  supervisord will attempt to raise the hard limit.  This may succeed if
  supervisord is run as root, otherwise the error is printed as before.
  Patch by Benoit Sigoure.

- Add a boolean program option ``killasgroup``, defaulting to false,
  if true when resorting to send SIGKILL to stop/terminate the process
  send it to its whole process group instead to take care of possible
  children as well and not leave them behind.  Patch by Samuele Pedroni.

- Environment variables may now be used in the configuration file
  for options that support string expansion.  Patch by Aleksey Sivokon.

- Fixed a race condition where supervisord might not act on a signal sent
  to it.  Thanks to Adar Dembo for reporting the issue and supplying the
  initial patch.

- Updated the output of ``echo_supervisord_conf`` to fix typos and
  improve comments.  Thanks to Jens Rantil for noticing these.

- Fixed a possible 500 Server Error from the web interface.  This was
  observed when using Supervisor on a domain socket behind Nginx, where
  Supervisor would raise an exception because REMOTE_ADDR was not set.
  Patch by David Bennett.

3.0a10 (2011-03-30)
-------------------

- Fixed the stylesheet of the web interface so the footer line won't overlap
  a long process list.  Thanks to Derek DeVries for the patch.

- Allow rpc interface plugins to register new events types.

- Bug fix for FCGI sockets not getting cleaned up when the ``reload`` command
  is issued from supervisorctl.  Also, the default behavior has changed for
  FCGI sockets.  They are now closed whenever the number of running processes
  in a group hits zero.  Previously, the sockets were kept open unless a
  group-level stop command was issued.

- Better error message when HTTP server cannot reverse-resolve a hostname to
  an IP address.  Previous behavior: show a socket error.  Current behavior:
  spit out a suggestion to stdout.

- Environment variables set via ``environment=`` value within
  ``[supervisord]`` section had no effect.  Thanks to Wyatt Baldwin
  for a patch.

- Fix bug where stopping process would cause process output that happened
  after the stop request was issued to be lost.  See
  https://github.com/Supervisor/supervisor/issues/11.

- Moved 2.X change log entries into ``HISTORY.txt``.

- Converted ``CHANGES.txt`` and ``README.txt`` into proper ReStructuredText
  and included them in the ``long_description`` in ``setup.py``.

- Added a tox.ini to the package (run via ``tox`` in the package dir).  Tests
  supervisor on multiple Python versions.

3.0a9 (2010-08-13)
------------------

- Use rich comparison methods rather than __cmp__ to sort process configs and
  process group configs to better straddle Python versions.  (thanks to
  Jonathan Riboux for identifying the problem and supplying an initial
  patch).

- Fixed test_supervisorctl.test_maintail_dashf test for Python 2.7.  (thanks
  to Jonathan Riboux for identifying the problem and supplying an initial
  patch).

- Fixed the way that supervisor.datatypes.url computes a "good" URL
  for compatibility with Python 2.7 and Python >= 2.6.5.  URLs with
  bogus "schemes://" will now be accepted as a version-straddling
  compromise (before they were rejected before supervisor would
  start).  (thanks to Jonathan Riboux for identifying the problem
  and supplying an initial patch).

- Add a ``-v`` / ``--version`` option to supervisord: Print the
  supervisord version number out to stdout and exit.  (Roger Hoover)

- Import iterparse from xml.etree when available (eg: Python 2.6).  Patch
  by Sidnei da Silva.

- Fixed the url to the supervisor-users mailing list.  Patch by
  Sidnei da Silva

- When parsing "environment=" in the config file, changes introduced in
  3.0a8 prevented Supervisor from parsing some characters commonly
  found in paths unless quoting was used as in this example::

    environment=HOME='/home/auser'

  Supervisor once again allows the above line to be written as::

    environment=HOME=/home/auser

  Alphanumeric characters, "_", "/", ".", "+", "-", "(", ")", and ":" can all
  be used as a value without quoting. If any other characters are needed in
  the value, please quote it as in the first example above.  Thanks to Paul
  Heideman for reporting this issue.

- Supervisor will now look for its config file in locations relative to the
  executable path, allowing it to be used more easily in virtual
  environments.  If sys.argv[0] is ``/path/to/venv/bin/supervisorctl``,
  supervisor will now look for it's config file in
  ``/path/to/venv/etc/supervisord.conf`` and
  ``/path/to/venv/supervisord.conf`` in addition to the other standard
  locations.  Patch by Chris Rossi.

3.0a8 (2010-01-20)
------------------

- Don't cleanup file descriptors on first supervisord invocation:
  this is a lame workaround for Snow Leopard systems that use
  libdispatch and are receiving "Illegal instruction" messages at
  supervisord startup time.  Restarting supervisord via
  "supervisorctl restart" may still cause a crash on these systems.

- Got rid of Medusa hashbang headers in various files to ease RPM
  packaging.

- Allow umask to be 000 (patch contributed by Rowan Nairn).

- Fixed a bug introduced in 3.0a7 where supervisorctl wouldn't ask
  for a username/password combination properly from a
  password-protected supervisord if it wasn't filled in within the
  "[supervisorctl]" section username/password values.  It now
  properly asks for a username and password.

- Fixed a bug introduced in 3.0a7 where setup.py would not detect the
  Python version correctly.  Patch by Daniele Paolella.

- Fixed a bug introduced in 3.0a7 where parsing a string of key/value
  pairs failed on Python 2.3 due to use of regular expression syntax
  introduced in Python 2.4.

- Removed the test suite for the ``memmon`` console script, which was
  moved to the Superlance package in 3.0a7.

- Added release dates to CHANGES.txt.

- Reloading the config for an fcgi process group did not close the fcgi
  socket - now, the socket is closed whenever the group is stopped as a unit
  (including during config update). However, if you stop all the processes
  in a group individually, the socket will remain open to allow for graceful
  restarts of FCGI daemons.  (Roger Hoover)

- Rereading the config did not pick up changes to the socket parameter in a
  fcgi-program section.  (Roger Hoover)

- Made a more friendly exception message when a FCGI socket cannot be
  created.  (Roger Hoover)

- Fixed a bug where the --serverurl option of supervisorctl would not
  accept a URL with a "unix" scheme.  (Jason Kirtland)

- Running the tests now requires the "mock" package.  This dependency has
  been added to "tests_require" in setup.py.  (Roger Hoover)

- Added support for setting the ownership and permissions for an FCGI socket.
  This is done using new "socket_owner" and "socket_mode" options in an
  [fcgi-program:x] section.  See the manual for details.  (Roger Hoover)

- Fixed a bug where the FCGI socket reference count was not getting
  decremented on spawn error.  (Roger Hoover)

- Fixed a Python 2.6 deprecation warning on use of the "sha" module.

- Updated ez_setup.py to one that knows about setuptools 0.6c11.

- Running "supervisorctl shutdown" no longer dumps a Python backtrace
  when it can't connect to supervisord on the expected socket.  Thanks
  to Benjamin Smith for reporting this.

- Removed use of collections.deque in our bundled version of asynchat
  because it broke compatibility with Python 2.3.

- The sample configuration output by "echo_supervisord_conf" now correctly
  shows the default for "autorestart" as "unexpected".  Thanks to
  William Dode for noticing it showed the wrong value.

3.0a7 (2009-05-24)
------------------

- We now bundle our own patched version of Medusa contributed by Jason
  Kirtland to allow Supervisor to run on Python 2.6.  This was done
  because Python 2.6 introduced backwards incompatible changes to
  asyncore and asynchat in the stdlib.

- The console script ``memmon``, introduced in Supervisor 3.0a4, has
  been moved to Superlance (http://pypi.python.org/pypi/superlance).
  The Superlance package contains other useful monitoring tools designed
  to run under Supervisor.

- Supervisorctl now correctly interprets all of the error codes that can
  be returned when starting a process.  Patch by Francesc Alted.

- New ``stdout_events_enabled`` and ``stderr_events_enabled`` config options
  have been added to the ``[program:x]``, ``[fcgi-program:x]``, and
  ``[eventlistener:x]`` sections.  These enable the emitting of new
  PROCESS_LOG events for a program.  If unspecified, the default is False.

  If enabled for a subprocess, and data is received from the stdout or
  stderr of the subprocess while not in the special capture mode used by
  PROCESS_COMMUNICATION, an event will be emitted.

  Event listeners can subscribe to either PROCESS_LOG_STDOUT or
  PROCESS_LOG_STDERR individually, or PROCESS_LOG for both.

- Values for subprocess environment variables specified with environment=
  in supervisord.conf can now be optionally quoted, allowing them to
  contain commas.  Patch by Tim Godfrey.

- Added a new event type, REMOTE_COMMUNICATION, that is emitted by a new
  RPC method, supervisor.sendRemoteCommEvent().

- Patch for bug #268 (KeyError on ``here`` expansion for
  stdout/stderr_logfile) from David E. Kindred.

- Add ``reread``, ``update``, and ``avail`` commands based on Anders
  Quist's ``online_config_reload.diff`` patch.  This patch extends
  the "add" and "drop" commands with automagical behavior::

    In supervisorctl:

      supervisor> status
      bar                              RUNNING    pid 14864, uptime 18:03:42
      baz                              RUNNING    pid 23260, uptime 0:10:16
      foo                              RUNNING    pid 14866, uptime 18:03:42
      gazonk                           RUNNING    pid 23261, uptime 0:10:16
      supervisor> avail
      bar                              in use    auto      999:999
      baz                              in use    auto      999:999
      foo                              in use    auto      999:999
      gazonk                           in use    auto      999:999
      quux                             avail     auto      999:999

    Now we add this to our conf:

      [group:zegroup]
      programs=baz,gazonk

    Then we reread conf:

      supervisor> reread
      baz: disappeared
      gazonk: disappeared
      quux: available
      zegroup: available
      supervisor> avail
      bar                              in use    auto      999:999
      foo                              in use    auto      999:999
      quux                             avail     auto      999:999
      zegroup:baz                      avail     auto      999:999
      zegroup:gazonk                   avail     auto      999:999
      supervisor> status
      bar                              RUNNING    pid 14864, uptime 18:04:18
      baz                              RUNNING    pid 23260, uptime 0:10:52
      foo                              RUNNING    pid 14866, uptime 18:04:18
      gazonk                           RUNNING    pid 23261, uptime 0:10:52

    The magic make-it-so command:

      supervisor> update
      baz: stopped
      baz: removed process group
      gazonk: stopped
      gazonk: removed process group
      zegroup: added process group
      quux: added process group
      supervisor> status
      bar                              RUNNING    pid 14864, uptime 18:04:43
      foo                              RUNNING    pid 14866, uptime 18:04:43
      quux                             RUNNING    pid 23561, uptime 0:00:02
      zegroup:baz                      RUNNING    pid 23559, uptime 0:00:02
      zegroup:gazonk                   RUNNING    pid 23560, uptime 0:00:02
      supervisor> avail
      bar                              in use    auto      999:999
      foo                              in use    auto      999:999
      quux                             in use    auto      999:999
      zegroup:baz                      in use    auto      999:999
      zegroup:gazonk                   in use    auto      999:999

- Fix bug with symptom "KeyError: 'process_name'" when using a logfile name
  including documented``process_name`` Python string expansions.

- Tab completions in the supervisorctl shell, and a foreground mode for
  Supervisor, implemented as a part of GSoC.  The supervisorctl program now
  has a ``fg`` command, which makes it possible to supply inputs to a
  process, and see its output/error stream in real time.

- Process config reloading implemented by Anders Quist.  The
  supervisorctl program now has the commands "add" and "drop".
  "add <programname>" adds the process group implied by <programname>
  in the config file.  "drop <programname>" removes the process
  group from the running configuration (it must already be stopped).
  This makes it possible to add processes to and remove processes from
  a running supervisord without restarting the supervisord process.

- Fixed a bug where opening the HTTP servers would fail silently
  for socket errors other than errno.EADDRINUSE.

- Thanks to Dave Peticolas, using "reload" against a supervisord
  that is running in the background no longer causes supervisord
  to crash.

- Configuration options for logfiles now accept mixed case reserved
  words (e.g. "AUTO" or "auto") for consistency with other options.

- childutils.eventdata was buggy, it could not deal with carriage returns
  in data.  See http://www.plope.com/software/collector/257.  Thanks
  to Ian Bicking.

- Per-process exitcodes= configuration now will not accept exit
  codes that are not 8-bit unsigned integers (supervisord will not
  start when one of the exit codes is outside the range of 0 - 255).

- Per-process ``directory`` value can now contain expandable values like
  ``%(here)s``. (See http://www.plope.com/software/collector/262).

- Accepted patch from Roger Hoover to allow for a new sort of
  process group: "fcgi-program".  Adding one of these to your
  supervisord.conf allows you to control fastcgi programs.  FastCGI
  programs cannot belong to heterogenous groups.

  The configuration for FastCGI programs is the same as regular programs
  except an additional "socket" parameter.  Substitution happens on the
  socket parameter with the ``here`` and ``program_name`` variables::

   [fcgi-program:fcgi_test]
   ;socket=tcp://localhost:8002
   socket=unix:///path/to/fcgi/socket

- Supervisorctl now supports a plugin model for supervisorctl
  commands.

- Added the ability to retrieve supervisord's own pid through
  supervisor.getPID() on the XML-RPC interface or a new
  "pid" command on supervisorctl.

3.0a6 (2008-04-07)
------------------

- The RotatingFileLogger had a race condition in its doRollover
  method whereby a file might not actually exist despite a call to
  os.path.exists on the line above a place where we try to remove
  it.  We catch the exception now and ignore the missing file.

3.0a5 (2008-03-13)
------------------

- Supervisorctl now supports persistent readline history.  To
  enable, add "history_file = <pathname>" to the ``[supervisorctl]``
  section in your supervisord.conf file.

- Multiple commands may now be issued on one supervisorctl command
  line, e.g. "restart prog; tail -f prog".  Separate commands with a
  single semicolon; they will be executed in order as you would
  expect.

3.0a4 (2008-01-30)
------------------

- 3.0a3 broke Python 2.3 backwards compatibility.

- On Debian Sarge, one user reported that a call to
  options.mktempfile would fail with an "[Errno 9] Bad file
  descriptor" at supervisord startup time.  I was unable to
  reproduce this, but we found a workaround that seemed to work for
  him and it's included in this release.  See
  http://www.plope.com/software/collector/252 for more information.
  Thanks to William Dode.

- The fault ``ALREADY_TERMINATED`` has been removed.  It was only raised by
  supervisor.sendProcessStdin().  That method now returns ``NOT_RUNNING``
  for parity with the other methods. (Mike Naberezny)

- The fault TIMED_OUT has been removed.  It was not used.

- Supervisor now depends on meld3 0.6.4, which does not compile its
  C extensions by default, so there is no more need to faff around
  with NO_MELD3_EXTENSION_MODULES during installation if you don't
  have a C compiler or the Python development libraries on your
  system.

- Instead of making a user root around for the sample.conf file,
  provide a convenience command "echo_supervisord_conf", which he can
  use to echo the sample.conf to his terminal (and redirect to a file
  appropriately).  This is a new user convenience (especially one who
  has no Python experience).

- Added ``numprocs_start`` config option to ``[program:x]`` and
  ``[eventlistener:x]`` sections.  This is an offset used to compute
  the first integer that ``numprocs`` will begin to start from.
  Contributed by Antonio Beamud Montero.

- Added capability for ``[include]`` config section to config format.
  This section must contain a single key "files", which must name a
  space-separated list of file globs that will be included in
  supervisor's configuration.  Contributed by Ian Bicking.

- Invoking the ``reload`` supervisorctl command could trigger a bug in
  supervisord which caused it to crash.  See
  http://www.plope.com/software/collector/253 .  Thanks to William Dode for
  a bug report.

- The ``pidproxy`` script was made into a console script.

- The ``password`` value in both the ``[inet_http_server]`` and
  ``[unix_http_server]`` sections can now optionally be specified as a SHA
  hexdigest instead of as cleartext.  Values prefixed with ``{SHA}`` will be
  considered SHA hex digests.  To encrypt a password to a form suitable for
  pasting into the configuration file using Python, do, e.g.::

     >>> import sha
     >>> '{SHA}' + sha.new('thepassword').hexdigest()
     '{SHA}82ab876d1387bfafe46cc1c8a2ef074eae50cb1d'

- The subtypes of the events PROCESS_STATE_CHANGE (and
  PROCESS_STATE_CHANGE itself) have been removed, replaced with a
  simpler set of PROCESS_STATE subscribable event types.

  The new event types are:

    PROCESS_STATE_STOPPED
    PROCESS_STATE_EXITED
    PROCESS_STATE_STARTING
    PROCESS_STATE_STOPPING
    PROCESS_STATE_BACKOFF
    PROCESS_STATE_FATAL
    PROCESS_STATE_RUNNING
    PROCESS_STATE_UNKNOWN
    PROCESS_STATE # abstract

  PROCESS_STATE_STARTING replaces:

    PROCESS_STATE_CHANGE_STARTING_FROM_STOPPED
    PROCESS_STATE_CHANGE_STARTING_FROM_BACKOFF
    PROCESS_STATE_CHANGE_STARTING_FROM_EXITED
    PROCESS_STATE_CHANGE_STARTING_FROM_FATAL

  PROCESS_STATE_RUNNING replaces
  PROCESS_STATE_CHANGE_RUNNING_FROM_STARTED

  PROCESS_STATE_BACKOFF replaces
  PROCESS_STATE_CHANGE_BACKOFF_FROM_STARTING

  PROCESS_STATE_STOPPING replaces:

    PROCESS_STATE_CHANGE_STOPPING_FROM_RUNNING
    PROCESS_STATE_CHANGE_STOPPING_FROM_STARTING

  PROCESS_STATE_EXITED replaces
  PROCESS_STATE_CHANGE_EXITED_FROM_RUNNING

  PROCESS_STATE_STOPPED replaces
  PROCESS_STATE_CHANGE_STOPPED_FROM_STOPPING

  PROCESS_STATE_FATAL replaces
  PROCESS_STATE_CHANGE_FATAL_FROM_BACKOFF

  PROCESS_STATE_UNKNOWN replaces PROCESS_STATE_CHANGE_TO_UNKNOWN

  PROCESS_STATE replaces PROCESS_STATE_CHANGE

  The PROCESS_STATE_CHANGE_EXITED_OR_STOPPED abstract event is gone.

  All process state changes have at least "processname",
  "groupname", and "from_state" (the name of the previous state) in
  their serializations.

  PROCESS_STATE_EXITED additionally has "expected" (1 or 0) and "pid"
  (the process id) in its serialization.

  PROCESS_STATE_RUNNING, PROCESS_STATE_STOPPING,
  PROCESS_STATE_STOPPED additionally have "pid" in their
  serializations.

  PROCESS_STATE_STARTING and PROCESS_STATE_BACKOFF have "tries" in
  their serialization (initially "0", bumped +1 each time a start
  retry happens).

- Remove documentation from README.txt, point people to
  http://supervisord.org/manual/ .

- The eventlistener request/response protocol has changed.  OK/FAIL
  must now be wrapped in a RESULT envelope so we can use it for more
  specialized communications.

  Previously, to signify success, an event listener would write the string
  ``OK\n`` to its stdout.  To signify that the event was seen but couldn't
  be handled by the listener and should be rebuffered, an event listener
  would write the string ``FAIL\n`` to its stdout.

  In the new protocol, the listener must write the string::

    RESULT {resultlen}\n{result}

  For example, to signify OK::

    RESULT 2\nOK

  To signify FAIL::

    RESULT 4\nFAIL

  See the scripts/sample_eventlistener.py script for an example.

- To provide a hook point for custom results returned from event
  handlers (see above) the [eventlistener:x] configuration sections
  now accept a "result_handler=" parameter,
  e.g. "result_handler=supervisor.dispatchers:default_handler" (the
  default) or "handler=mypackage:myhandler".  The keys are pkgutil
  "entry point" specifications (importable Python function names).
  Result handlers must be callables which accept two arguments: one
  named "event" which represents the event, and the other named
  "result", which represents the listener's result.  A result
  handler either executes successfully or raises an exception.  If
  it raises a supervisor.dispatchers.RejectEvent exception, the
  event will be rebuffered, and the eventhandler will be placed back
  into the ACKNOWLEDGED state.  If it raises any other exception,
  the event handler will be placed in the UNKNOWN state.  If it does
  not raise any exception, the event is considered successfully
  processed.  A result handler's return value is ignored.  Writing a
  result handler is a "in case of emergency break glass" sort of
  thing, it is not something to be used for arbitrary business code.
  In particular, handlers *must not block* for any appreciable
  amount of time.

  The standard eventlistener result handler
  (supervisor.dispatchers:default_handler) does nothing if it receives an
  "OK" and will raise a supervisor.dispatchers.RejectEvent exception if it
  receives any other value.

- Supervisord now emits TICK events, which happen every N seconds.
  Three types of TICK events are available: TICK_5 (every five
  seconds), TICK_60 (every minute), TICK_3600 (every hour).  Event
  listeners may subscribe to one of these types of events to perform
  every-so-often processing.  TICK events are subtypes of the EVENT
  type.

- Get rid of OSX platform-specific memory monitor and replace with
  memmon.py, which works on both Linux and Mac OS.  This script is
  now a console script named "memmon".

- Allow "web handler" (the handler which receives http requests from
  browsers visiting the web UI of supervisor) to deal with POST requests.

- RPC interface methods stopProcess(), stopProcessGroup(), and
  stopAllProcesses() now take an optional "wait" argument that defaults
  to True for parity with the start methods.

3.0a3 (2007-10-02)
------------------

- Supervisorctl now reports a better error message when the main supervisor
  XML-RPC namespace is not registered.  Thanks to Mike Orr for reporting
  this. (Mike Naberezny)

- Create ``scripts`` directory within supervisor package, move
  ``pidproxy.py`` there, and place sample event listener and comm event
  programs within the directory.

- When an event notification is buffered (either because a listener rejected
  it or because all listeners were busy when we attempted to send it
  originally), we now rebuffer it in a way that will result in it being
  retried earlier than it used to be.

- When a listener process exits (unexpectedly) before transitioning from the
  BUSY state, rebuffer the event that was being processed.

- supervisorctl ``tail`` command now accepts a trailing specifier: ``stderr``
  or ``stdout``, which respectively, allow a user to tail the stderr or
  stdout of the named process.  When this specifier is not provided, tail
  defaults to stdout.

- supervisor ``clear`` command now clears both stderr and stdout logs for the
  given process.

- When a process encounters a spawn error as a result of a failed execve or
  when it cannot setuid to a given uid, it now puts this info into the
  process' stderr log rather than its stdout log.

- The event listener protocol header now contains the ``server`` identifier,
  the ``pool`` that the event emanated from, and the ``poolserial`` as well
  as the values it previously contained (version, event name, serial, and
  length).  The server identifier is taken from the config file options value
  ``identifier``, the ``pool`` value is the name of the listener pool that
  this event emanates from, and the ``poolserial`` is a serial number
  assigned to the event local to the pool that is processing it.

- The event listener protocol header is now a sequence of key-value
  pairs rather than a list of positional values.  Previously, a
  representative header looked like::

    SUPERVISOR3.0 PROCESS_COMMUNICATION_STDOUT 30 22\n

  Now it looks like::

    ver:3.0 server:supervisor serial:21 ...

- Specific event payload serializations have changed.  All event
  types that deal with processes now include the pid of the process
  that the event is describing.  In event serialization "header"
  values, we've removed the space between the header name and the
  value and headers are now separated by a space instead of a line
  feed.  The names of keys in all event types have had underscores
  removed.

- Abandon the use of the Python stdlib ``logging`` module for speed
  and cleanliness purposes.  We've rolled our own.

- Fix crash on start if AUTO logging is used with a max_bytes of
  zero for a process.

- Improve process communication event performance.

- The process config parameters ``stdout_capturefile`` and
  ``stderr_capturefile`` are no longer valid.  They have been replaced with
  the ``stdout_capture_maxbytes`` and ``stderr_capture_maxbytes`` parameters,
  which are meant to be suffix-multiplied integers.  They both default to
  zero.  When they are zero, process communication event capturing is not
  performed.  When either is nonzero, the value represents the maximum number
  of bytes that will be captured between process event start and end tags.
  This change was to support the fact that we no longer keep capture data in
  a separate file, we just use a FIFO in RAM to maintain capture info.  For
  users whom don't care about process communication events, or whom haven't
  changed the defaults for ``stdout_capturefile`` or ``stderr_capturefile``,
  they needn't do anything to their configurations to deal with this change.

- Log message levels have been normalized.  In particular, process
  stdin/stdout is now logged at ``debug`` level rather than at ``trace``
  level (``trace`` level is now reserved for output useful typically for
  debugging supervisor itself).  See "Supervisor Log Levels" in the
  documentation for more info.

- When an event is rebuffered (because all listeners are busy or a
  listener rejected the event), the rebuffered event is now inserted
  in the head of the listener event queue.  This doesn't guarantee
  event emission in natural ordering, because if a listener rejects
  an event or dies while it's processing an event, it can take an
  arbitrary amount of time for the event to be rebuffered, and other
  events may be processed in the meantime.  But if pool listeners
  never reject an event or don't die while processing an event, this
  guarantees that events will be emitted in the order that they were
  received because if all listeners are busy, the rebuffered event
  will be tried again "first" on the next go-around.

- Removed EVENT_BUFFER_OVERFLOW event type.

- The supervisorctl xmlrpc proxy can now communicate with
  supervisord using a persistent HTTP connection.

- A new module "supervisor.childutils" was added.  This module
  provides utilities for Python scripts which act as children of
  supervisord.  Most notably, it contains an API method
  "getRPCInterface" allows you to obtain an xmlrpclib ServerProxy
  that is willing to communicate with the parent supervisor.  It
  also contains utility functions that allow for parsing of
  supervisor event listener protocol headers.  A pair of scripts
  (loop_eventgen.py and loop_listener.py) were added to the script
  directory that serve as examples about how to use the childutils
  module.

- A new envvar is added to child process environments:
  SUPERVISOR_SERVER_URL.  This contains the server URL for the
  supervisord running the child.

- An ``OK`` URL was added at ``/ok.html`` which just returns the string
  ``OK`` (can be used for up checks or speed checks via plain-old-HTTP).

- An additional command-line option ``--profile_options`` is accepted
  by the supervisord script for developer use::

    supervisord -n -c sample.conf --profile_options=cumulative,calls

  The values are sort_stats options that can be passed to the
  standard Python profiler's PStats sort_stats method.

  When you exit supervisor, it will print Python profiling output to
  stdout.

- If cElementTree is installed in the Python used to invoke
  supervisor, an alternate (faster, by about 2X) XML parser will be
  used to parse XML-RPC request bodies.  cElementTree was added as
  an "extras_require" option in setup.py.

- Added the ability to start, stop, and restart process groups to
  supervisorctl.  To start a group, use ``start groupname:*``.  To start
  multiple groups, use ``start groupname1:* groupname2:*``.  Equivalent
  commands work for "stop" and "restart". You can mix and match short
  processnames, fullly-specified group:process names, and groupsplats on the
  same line for any of these commands.

- Added ``directory`` option to process config.  If you set this
  option, supervisor will chdir to this directory before executing
  the child program (and thus it will be the child's cwd).

- Added ``umask`` option to process config.  If you set this option,
  supervisor will set the umask of the child program.  (Thanks to
  Ian Bicking for the suggestion).

- A pair of scripts ``osx_memmon_eventgen.py`` and `osx_memmon_listener.py``
  have been added to the scripts directory.  If they are used together as
  described in their comments, processes which are consuming "too much"
  memory will be restarted.  The ``eventgen`` script only works on OSX (my
  main development platform) but it should be trivially generalizable to
  other operating systems.

- The long form ``--configuration`` (-c) command line option for
  supervisord was broken.  Reported by Mike Orr.  (Mike Naberezny)

- New log level: BLAT (blather).  We log all
  supervisor-internal-related debugging info here.  Thanks to Mike
  Orr for the suggestion.

- We now allow supervisor to listen on both a UNIX domain socket and an inet
  socket instead of making them mutually exclusive.  As a result, the options
  "http_port", "http_username", "http_password", "sockchmod" and "sockchown"
  are no longer part of the ``[supervisord]`` section configuration. These
  have been supplanted by two other sections: ``[unix_http_server]`` and
  ``[inet_http_server]``.  You'll need to insert one or the other (depending
  on whether you want to listen on a UNIX domain socket or a TCP socket
  respectively) or both into your supervisord.conf file.  These sections have
  their own options (where applicable) for port, username, password, chmod,
  and chown.  See README.txt for more information about these sections.

- All supervisord command-line options related to "http_port",
  "http_username", "http_password", "sockchmod" and "sockchown" have
  been removed (see above point for rationale).

- The option that *used* to be ``sockchown`` within the ``[supervisord]``
  section (and is now named ``chown`` within the ``[unix_http_server]``
  section) used to accept a dot-separated user.group value.  The separator
  now must be a colon ":", e.g. "user:group".  Unices allow for dots in
  usernames, so this change is a bugfix.  Thanks to Ian Bicking for the bug
  report.

- If a '-c' option is not specified on the command line, both supervisord and
  supervisorctl will search for one in the paths ``./supervisord.conf`` ,
  ``./etc/supervisord.conf`` (relative to the current working dir when
  supervisord or supervisorctl is invoked) or in ``/etc/supervisord.conf``
  (the old default path).  These paths are searched in order, and supervisord
  and supervisorctl will use the first one found.  If none are found,
  supervisor will fail to start.

- The Python string expression ``%(here)s`` (referring to the directory in
  which the the configuration file was found) can be used within the
  following sections/options within the config file::

      unix_http_server:file
      supervisor:directory
      supervisor:logfile
      supervisor:pidfile
      supervisor:childlogdir
      supervisor:environment
      program:environment
      program:stdout_logfile
      program:stderr_logfile
      program:process_name
      program:command

- The ``--environment`` aka ``-b`` option was removed from the list of
  available command-line switches to supervisord (use "A=1 B=2
  bin/supervisord" instead).

- If the socket filename (the tail-end of the unix:// URL) was
  longer than 64 characters, supervisorctl would fail with an
  encoding error at startup.

- The ``identifier`` command-line argument was not functional.

- Fixed http://www.plope.com/software/collector/215 (bad error
  message in supervisorctl when program command not found on PATH).

- Some child processes may not have been shut down properly at
  supervisor shutdown time.

- Move to ZPL-derived (but not ZPL) license available from
  http://www.repoze.org/LICENSE.txt; it's slightly less restrictive
  than the ZPL (no servicemark clause).

- Spurious errors related to unclosed files ("bad file descriptor",
  typically) were evident at supervisord "reload" time (when using
  the "reload" command from supervisorctl).

- We no longer bundle ez_setup to bootstrap setuptools installation.

3.0a2 (2007-08-24)
------------------

- Fixed the README.txt example for defining the supervisor RPC
  interface in the configuration file.  Thanks to Drew Perttula.

- Fixed a bug where process communication events would not have the
  proper payload if the payload data was very short.

- when supervisord attempted to kill a process with SIGKILL after
  the process was not killed within "stopwaitsecs" using a "normal"
  kill signal, supervisord would crash with an improper
  AssertionError.  Thanks to Calvin Hendryx-Parker.

- On Linux, Supervisor would consume too much CPU in an effective
  "busywait" between the time a subprocess exited and the time at
  which supervisor was notified of its exit status.  Thanks to Drew
  Perttula.

- RPC interface behavior change: if the RPC method
  "sendProcessStdin" is called against a process that has closed its
  stdin file descriptor (e.g. it has done the equivalent of
  "sys.stdin.close(); os.close(0)"), we return a NO_FILE fault
  instead of accepting the data.

- Changed the semantics of the process configuration ``autorestart``
  parameter with respect to processes which move between the RUNNING and
  EXITED state.  ``autorestart`` was previously a boolean.  Now it's a
  trinary, accepting one of ``false``, ``unexpected``, or ``true``.  If it's
  ``false``, a process will never be automatically restarted from the EXITED
  state.  If it's ``unexpected``, a process that enters the EXITED state will
  be automatically restarted if it exited with an exit code that was not
  named in the process config's ``exitcodes`` list.  If it's ``true``, a
  process that enters the EXITED state will be automatically restarted
  unconditionally.  The default is now ``unexpected`` (it was previously
  ``true``).  The readdition of this feature is a reversion of the behavior
  change note in the changelog notes for 3.0a1 that asserted we never cared
  about the process' exit status when determining whether to restart it or
  not.

- setup.py develop (and presumably setup.py install) would fail under Python
  2.3.3, because setuptools attempted to import ``splituser`` from urllib2,
  and it didn't exist.

- It's now possible to use ``setup.py install`` and ``setup.py develop`` on
  systems which do not have a C compiler if you set the environment variable
  "NO_MELD3_EXTENSION_MODULES=1" in the shell in which you invoke these
  commands (versions of meld3 > 0.6.1 respect this envvar and do not try to
  compile optional C extensions when it's set).

- The test suite would fail on Python versions <= 2.3.3 because
  the "assertTrue" and "assertFalse" methods of unittest.TestCase
  didn't exist in those versions.

- The ``supervisorctl`` and ``supervisord`` wrapper scripts were disused in
  favor of using setuptools' ``console_scripts`` entry point settings.

- Documentation files and the sample configuration file are put into
  the generated supervisor egg's ``doc`` directory.

- Using the web interface would cause fairly dramatic memory
  leakage.  We now require a version of meld3 that does not appear
  to leak memory from its C extensions (0.6.3).

3.0a1 (2007-08-16)
------------------

- Default config file comment documented 10 secs as default for ``startsecs``
  value in process config, in reality it was 1 sec.  Thanks to Christoph
  Zwerschke.

- Make note of subprocess environment behavior in README.txt.
  Thanks to Christoph Zwerschke.

- New "strip_ansi" config file option attempts to strip ANSI escape
  sequences from logs for smaller/more readable logs (submitted by
  Mike Naberezny).

- The XML-RPC method supervisor.getVersion() has been renamed for
  clarity to supervisor.getAPIVersion().  The old name is aliased
  for compatibility but is deprecated and will be removed in a
  future version (Mike Naberezny).

- Improved web interface styling (Mike Naberezny, Derek DeVries)

- The XML-RPC method supervisor.startProcess() now checks that
  the file exists and is executable (Mike Naberezny).

- Two environment variables, "SUPERVISOR_PROCESS_NAME" and
  "SUPERVISOR_PROCESS_GROUP" are set in the environment of child
  processes, representing the name of the process and group in
  supervisor's configuration.

- Process state map change: a process may now move directly from the
  STARTING state to the STOPPING state (as a result of a stop
  request).

- Behavior change: if ``autorestart`` is true, even if a process exits with
  an "expected" exit code, it will still be restarted.  In the immediately
  prior release of supervisor, this was true anyway, and no one complained,
  so we're going to consider that the "officially correct" behavior from now
  on.

- Supervisor now logs subprocess stdout and stderr independently.
  The old program config keys "logfile", "logfile_backups" and
  "logfile_maxbytes" are superseded by "stdout_logfile",
  "stdout_logfile_backups", and "stdout_logfile_maxbytes".  Added
  keys include "stderr_logfile", "stderr_logfile_backups", and
  "stderr_logfile_maxbytes".  An additional "redirect_stderr" key is
  used to cause program stderr output to be sent to its stdout
  channel.  The keys "log_stderr" and "log_stdout" have been
  removed.

- ``[program:x]`` config file sections now represent "homgeneous process
  groups" instead of single processes.  A "numprocs" key in the section
  represents the number of processes that are in the group.  A "process_name"
  key in the section allows composition of the each process' name within the
  homogeneous group.

- A new kind of config file section, ``[group:x]`` now exists, allowing users
  to group heterogeneous processes together into a process group that can be
  controlled as a unit from a client.

- Supervisord now emits "events" at certain points in its normal
  operation.  These events include supervisor state change events,
  process state change events, and "process communication events".

- A new kind of config file section ``[eventlistener:x]`` now exists.  Each
  section represents an "event listener pool", which is a special kind of
  homogeneous process group.  Each process in the pool is meant to receive
  supervisor "events" via its stdin and perform some notification (e.g. send
  a mail, log, make an http request, etc.)

- Supervisord can now capture data between special tokens in
  subprocess stdout/stderr output and emit a "process communications
  event" as a result.

- Supervisor's XML-RPC interface may be extended arbitrarily by programmers.
  Additional top-level namespace XML-RPC interfaces can be added using the
  ``[rpcinterface:foo]`` declaration in the configuration file.

- New ``supervisor``-namespace XML-RPC methods have been added:
  getAPIVersion (returns the XML-RPC API version, the older
  "getVersion" is now deprecated), "startProcessGroup" (starts all
  processes in a supervisor process group), "stopProcessGroup"
  (stops all processes in a supervisor process group), and
  "sendProcessStdin" (sends data to a process' stdin file
  descriptor).

- ``supervisor``-namespace XML-RPC methods which previously accepted
  ony a process name as "name" (startProcess, stopProcess,
  getProcessInfo, readProcessLog, tailProcessLog, and
  clearProcessLog) now accept a "name" which may contain both the
  process name and the process group name in the form
  ``groupname:procname``.  For backwards compatibility purposes,
  "simple" names will also be accepted but will be expanded
  internally (e.g. if "foo" is sent as a name, it will be expanded
  to "foo:foo", representing the foo process within the foo process
  group).

- 2.X versions of supervisorctl will work against supervisor 3.0
  servers in a degraded fashion, but 3.X versions of supervisorctl
  will not work at all against supervisor 2.X servers.

2.2b1 (2007-03-31)
------------------

- Individual program configuration sections can now specify an
  environment.

- Added a 'version' command to supervisorctl.  This returns the
  version of the supervisor2 package which the remote supervisord
  process is using.

2.1 (2007-03-17)
----------------

- When supervisord was invoked more than once, and its configuration
  was set up to use a UNIX domain socket as the HTTP server, the
  socket file would be erased in error.  The symptom of this was
  that a subsequent invocation of supervisorctl could not find the
  socket file, so the process could not be controlled (it and all of
  its subprocesses would need to be killed by hand).

- Close subprocess file descriptors properly when a subprocess exits
  or otherwise dies.  This should result in fewer "too many open
  files to spawn foo" messages when supervisor is left up for long
  periods of time.

- When a process was not killable with a "normal" signal at shutdown
  time, too many "INFO: waiting for x to die" messages would be sent
  to the log until we ended up killing the process with a SIGKILL.
  Now a maximum of one every three seconds is sent up until SIGKILL
  time.  Thanks to Ian Bicking.

- Add an assertion: we never want to try to marshal None to XML-RPC
  callers.  Issue 223 in the collector from vgatto indicates that
  somehow a supervisor XML-RPC method is returning None (which
  should never happen), but I cannot identify how.  Maybe the
  assertion will give us more clues if it happens again.

- Supervisor would crash when run under Python 2.5 because the
  xmlrpclib.Transport class in Python 2.5 changed in a
  backward-incompatible way.  Thanks to Eric Westra for the bug
  report and a fix.

- Tests now pass under Python 2.5.

- Better supervisorctl reporting on stop requests that have a FAILED
  status.

- Removed duplicated code (readLog/readMainLog), thanks to Mike
  Naberezny.

- Added tailProcessLog command to the XML-RPC API.  It provides a
  more efficient way to tail logs than readProcessLog().  Use
  readProcessLog() to read chunks and tailProcessLog() to tail.
  (thanks to Mike Naberezny).

2.1b1 (2006-08-30)
------------------

- "supervisord -h" and "supervisorctl -h" did not work (traceback
  instead of showing help view (thanks to Damjan from Macedonia for
  the bug report).

- Processes which started successfully after failing to start
  initially are no longer reported in BACKOFF state once they are
  started successfully (thanks to Damjan from Macdonia for the bug
  report).

- Add new 'maintail' command to supervisorctl shell, which allows
  you to tail the 'main' supervisor log.  This uses a new
  readMainLog xmlrpc API.

- Various process-state-transition related changes, all internal.
  README.txt updated with new state transition map.

- startProcess and startAllProcesses xmlrpc APIs changed: instead of
  accepting a timeout integer, these accept a wait boolean (timeout
  is implied by process' "startsecs" configuration).  If wait is
  False, do not wait for startsecs.

Known issues:

- Code does not match state transition map.  Processes which are
  configured as autorestarting which start "successfully" but
  subsequently die after 'startsecs' go through the transitions
  RUNNING -> BACKOFF -> STARTING instead of the correct transitions
  RUNNING -> EXITED -> STARTING.  This has no real negative effect,
  but should be fixed for correctness.

2.0 (2006-08-30)
----------------

- pidfile written in daemon mode had incorrect pid.

- supervisorctl: tail (non -f) did not pass through proper error
  messages when supplied by the server.

- Log signal name used to kill processes at debug level.

- supervisorctl "tail -f" didn't work with supervisorctl sections
  configured with an absolute unix:// URL

- New "environment" config file option allows you to add environment
  variable values to supervisord environment from config file.

2.0b1 (2006-07-12)
------------------

- Fundamental rewrite based on 1.0.7, use distutils (only) for
  installation, use ConfigParser rather than ZConfig, use HTTP for
  wire protocol, web interface, less lies in supervisorctl.

1.0.7 (2006-07-11)
------------------

- Don't log a waitpid error if the error value is "no children".

- Use select() against child file descriptor pipes and bump up select
  timeout appropriately.

1.0.6 (2005-11-20)
------------------

- Various tweaks to make run more effectively on Mac OS X
  (including fixing tests to run there, no more "error reading
  from fd XXX" in logtail output, reduced disk/CPU usage as a
  result of not writing to log file unnecessarily on Mac OS).

1.0.5 (2004-07-29)
------------------

- Short description: In previous releases, managed programs that
  created voluminous stdout/stderr output could run more slowly
  than usual when invoked under supervisor, now they do not.

  Long description: The supervisord manages child output by
  polling pipes related to child process stderr/stdout.  Polling
  operations are performed in the mainloop, which also performs a
  'select' on the filedescriptor(s) related to client/server
  operations.  In prior releases, the select timeout was set to 2
  seconds.  This release changes the timeout to 1/10th of a second
  in order to keep up with client stdout/stderr output.

  Gory description: On Linux, at least, there is a pipe buffer
  size fixed by the kernel of somewhere between 512 - 4096 bytes;
  when a child process writes enough data to fill the pipe buffer,
  it will block on further stdout/stderr output until supervisord
  comes along and clears out the buffer by reading bytes from the
  pipe within the mainloop.  We now clear these buffers much more
  quickly than we did before due to the increased frequency of
  buffer reads in the mainloop; the timeout value of 1/10th of a
  second seems to be fast enough to clear out the buffers of child
  process pipes when managing programs on even a very fast system
  while still enabling the supervisord process to be in a sleeping
  state for most of the time.

1.0.4 or "Alpha 4" (2004-06-30)
-------------------------------

- Forgot to update version tag in configure.py, so the supervisor version
  in a3 is listed as "1.0.1", where it should be "1.0.3".  a4 will be
  listed as "1.0.4'.

- Instead of preventing a process from starting if setuid() can't
  be called (if supervisord is run as nonroot, for example), just log
  the error and proceed.

1.0.3 or "Alpha 3" (2004-05-26)
-------------------------------

- The daemon could chew up a lot of CPU time trying to select()
  on real files (I didn't know select() failed to block when a file
  is at EOF).  Fixed by polling instead of using select().

- Processes could "leak" and become zombies due to a bug in
  reaping dead children.

- supervisord now defaults to daemonizing itself.

- 'daemon' config file option and -d/--daemon command-line option
  removed from supervisord acceptable options.  In place of these
  options, we now have a 'nodaemon' config file option and a
  -n/--nodaemon command-line option.

- logtail now works.

- pidproxy changed slightly to reap children synchronously.

- in alpha2 changelist, supervisord was reported to have a
  "noauth" command-line option.  This was not accurate.  The way
  to turn off auth on the server is to disinclude the "passwdfile"
  config file option from the server config file.  The client
  however does indeed still have a noauth option, which prevents
  it from ever attempting to send authentication credentials to
  servers.

- ZPL license added for ZConfig to LICENSE.txt

1.0.2 or "Alpha 2" (Unreleased)
-------------------------------

- supervisorctl and supervisord no longer need to run on the same machine
  due to the addition of internet socket support.

- supervisorctl and supervisord no longer share a common configuration
  file format.

- supervisorctl now uses a persistent connection to supervisord
  (as opposed to creating a fresh connection for each command).

- SRP (Secure Remote Password) authentication is now a supported form
  of access control for supervisord.  In supervisorctl interactive mode,
  by default, users will be asked for credentials when attempting to
  talk to a supervisord that requires SRP authentication.

- supervisord has a new command-line option and configuration file
  option for specifying "noauth" mode, which signifies that it
  should not require authentication from clients.

- supervisorctl has a new command-line option and configuration
  option for specifying "noauth" mode, which signifies that it
  should never attempt to send authentication info to servers.

- supervisorctl has new commands: open: opens a connection to a new
  supervisord; close: closes the current connection.

- supervisorctl's "logtail" command now retrieves log data from
  supervisord's log file remotely (as opposed to reading it
  directly from a common filesystem).  It also no longer emulates
  "tail -f", it just returns <n> lines of the server's log file.

- The supervisord/supervisorctl wire protocol now has protocol versioning
  and is documented in "protocol.txt".

- "configfile" command-line override -C changed to -c

- top-level section name for supervisor schema changed to 'supervisord'
  from 'supervisor'

- Added 'pidproxy' shim program.

Known issues in alpha 2:

- If supervisorctl loses a connection to a supervisord or if the
  remote supervisord crashes or shuts down unexpectedly, it is
  possible that any supervisorctl talking to it will "hang"
  indefinitely waiting for data.  Pressing Ctrl-C will allow you
  to restart supervisorctl.

- Only one supervisorctl process may talk to a given supervisord
  process at a time.  If two supervisorctl processes attempt to talk
  to the same supervisord process, one will "win" and the other will
  be disconnected.

- Sometimes if a pidproxy is used to start a program, the pidproxy
  program itself will "leak".

1.0.0 or "Alpha 1" (Unreleased)
-------------------------------

Initial release.
