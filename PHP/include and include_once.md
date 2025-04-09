In general, it’s probably best to stick with ``include_once`` and ignore the basic ``include`` statement. That way, you will never have the problem of files being included multiple times.

A potential problem with include and include_once is that PHP will only attempt to include the requested file. Program execution continues even if the file is not found. When it is absolutely essential to include a file, require it.
use ``require_once``

