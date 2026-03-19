logging日志有五个级别，分别是debug，info，warning，error，critical
默认等级是info

logger是自定义的处理日志的对象，比使用logging更加的灵活

## Handler

- StreamHandler：如果未指定流，则使用 sys.stderr。
- RotatingFileHandler：打开指定的文件并将其用作日志记录的流，可以设置文件的大小和数量。