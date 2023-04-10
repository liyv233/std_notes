



# excel数据导入

~~~java
    public boolean insertFromExcel(String tableName, MultipartFile file,String needRow) {


        Workbook workbook;
        try {
            workbook = WorkbookFactory.create(file.getInputStream());
        }  catch (Exception e) {
            System.out.println(e.fillInStackTrace());
            return false ;

        }
        Sheet sheet = workbook.getSheetAt(0);
                Row headerRow = sheet.getRow(0);
        int[] columns = null;
                List<Object[]> rows = new ArrayList<>();
                for (int i = 1; i <= sheet.getLastRowNum(); i++) {
                    Row row = sheet.getRow(i);
                    Object[] rowData = new Object[headerRow.getLastCellNum()];
                    for (int j = 0; j < headerRow.getLastCellNum(); j++) {
                        Cell cell = row.getCell(j);
                        switch (cell.getCellTypeEnum()) {
                            case STRING:
                                rowData[j] = cell.getStringCellValue();
                                if  (rowData[j].equals("null")) rowData[j] = null ;
                                break;
                            case NUMERIC:
                                rowData[j] = cell.getNumericCellValue();
                                break;
                            case BOOLEAN:
                                rowData[j] = cell.getBooleanCellValue();
                                break;
                            case BLANK:
                                rowData[j] = null;
                                break;
                        }
                    }
                    rows.add(rowData);
                }

                StringBuilder sqlBuilder = new StringBuilder("INSERT INTO ");
                sqlBuilder.append(tableName).append(" (");
                for (int i = 0; i < headerRow.getLastCellNum(); i++) {
                    if (i > 0) {
                        sqlBuilder.append(", ");
                    }
                    sqlBuilder.append(headerRow.getCell(i).getStringCellValue());
                }
                sqlBuilder.append(") VALUES (");
                for (int i = 0; i < headerRow.getLastCellNum(); i++) {
                    if (i > 0) {
                        sqlBuilder.append(", ");
                    }
                    sqlBuilder.append("?");
                }
                sqlBuilder.append(")");

                jdbcTemplate.batchUpdate(sqlBuilder.toString(), rows);
                return true ;
            }
        }
~~~



需求：根据excel文件，动态将文件中的某几列导入进数据库中

~~~java
public boolean insertFromExcel(String tableName, MultipartFile file, String needColumns) throws IOException {
    Workbook workbook;
    try {
        workbook = WorkbookFactory.create(file.getInputStream());
    } catch (Exception e) {
        System.out.println(e.fillInStackTrace());
        return false;
    }
    Sheet sheet = workbook.getSheetAt(0);
    Row headerRow = sheet.getRow(0);
    String[] columnsArr = needColumns.split(",");
    int[] columns = new int[columnsArr.length];
    for (int i = 0; i < columnsArr.length; i++) {
        columns[i] = Integer.parseInt(columnsArr[i].trim()) - 1;
    }
    List<Object[]> rows = new ArrayList<>();
    for (int i = 1; i <= sheet.getLastRowNum(); i++) {
        Row row = sheet.getRow(i);
        Object[] rowData = new Object[columns.length];
        for (int j = 0; j < columns.length; j++) {
            Cell cell = row.getCell(columns[j]);
            switch (cell.getCellTypeEnum()) {
                case STRING:
                    rowData[j] = cell.getStringCellValue();
                    if (rowData[j].equals("null")) {
                        rowData[j] = null;
                    }
                    break;
                case NUMERIC:
                    rowData[j] = cell.getNumericCellValue();
                    break;
                case BOOLEAN:
                    rowData[j] = cell.getBooleanCellValue();
                    break;
                case BLANK:
                    rowData[j] = null;
                    break;
            }
        }
        rows.add(rowData);
    }
    StringBuilder sqlBuilder = new StringBuilder("INSERT INTO ");
    sqlBuilder.append(tableName).append(" (");
    for (int i = 0; i < columns.length; i++) {
        if (i > 0) {
            sqlBuilder.append(", ");
        }
        sqlBuilder.append(headerRow.getCell(columns[i]).getStringCellValue());
    }
    sqlBuilder.append(") VALUES (");
    for (int i = 0; i < columns.length; i++) {
        if (i > 0) {
            sqlBuilder.append(", ");
        }
        sqlBuilder.append("?");
    }
    sqlBuilder.append(")");
    jdbcTemplate.batchUpdate(sqlBuilder.toString(), rows);
    return true;
}
~~~

- `needRow` 参数，用于指定需要插入数据库的列。如果该参数为 null 或空字符串，则默认插入所有列。如果该参数不为空，则将其按逗号分隔成数组，并遍历该数组，查找对应的列索引。然后在后续代码中，只处理需要插入的列，忽略其他列。具体实现是在读取每一行数据时，只处理需要插入的列。在构建 SQL 语句时，也只包含需要插入的列。同时，新增了一个辅助方法 `contains()`，用于判断一个整型数组是否包含某个值。
- file：包含数据信息和列信息的excel文件：第一行为列名
- tableName：需要插入数据库的表名



在不改变功能的情况下，我们可以尝试通过优化代码来减小时间复杂度。具体优化如下：

1. 在循环中避免重复调用方法，如sheet.getLastRowNum()和headerRow.getCell()，可以将它们的返回值存储起来。
2. 在生成SQL语句时，使用StringBuilder代替String拼接，可以减少不必要的字符串对象的创建和销毁。
3. 使用PreparedStatement代替Statement，可以预编译SQL语句，提高执行效率。
4. 将多次执行的jdbcTemplate.batchUpdate()改为只执行一次，可以减少与数据库的交互次数，提高执行效率。 综上所述，以下是对代码的优化实现：

~~~java
public boolean insertFromExcel(String tableName, MultipartFile file, String needColumns) {
    Workbook workbook;
    try {
        // 从上传的Excel文件中获取工作簿对象
        workbook = WorkbookFactory.create(file.getInputStream());
    } catch (Exception e) {
        System.out.println(e.fillInStackTrace());
        return false;
    }
    // 获取Excel文件的第一个Sheet
    Sheet sheet = workbook.getSheetAt(0);
    // 获取Sheet的第一行，即表头
    Row headerRow = sheet.getRow(0);
    // 获取Sheet中的数据总行数
    int lastRowNum = sheet.getLastRowNum();
    // 获取表头总列数
    int columnCount = headerRow.getLastCellNum();
    // 需要导入的列索引
    int[] columns;
    if (needColumns == null) {
        // 如果没有指定需要导入的列，则默认导入全部列
        columns = new int[columnCount];
        for (int i = 0; i < columnCount; i++) {
            columns[i] = i;
        }
    } else {
        // 如果指定了需要导入的列，则按照指定的列顺序导入数据
        String[] columnsArr = needColumns.split(",");
        columns = new int[columnsArr.length];
        for (int i = 0; i < columnsArr.length; i++) {
            columns[i] = Integer.parseInt(columnsArr[i].trim()) - 1;
        }
    }
    // 用于存储导入数据的列表
    List<Object[]> rows = new ArrayList<>(lastRowNum - 1);
    // 遍历Sheet中的每一行数据
    for (int i = 1; i <= lastRowNum; i++) {
        Row row = sheet.getRow(i);
        // 用于存储当前行需要导入的数据
        Object[] rowData = new Object[columns.length];
        for (int j = 0; j < columns.length; j++) {
            Cell cell = row.getCell(columns[j]);
            // 根据单元格类型将单元格值转换为对应的Java类型
            switch (cell.getCellTypeEnum()) {
                case STRING:
                    String value = cell.getStringCellValue();
                    rowData[j] = value.equalsIgnoreCase("null") ? null : value;
                    break;
                case NUMERIC:
                    rowData[j] = cell.getNumericCellValue();
                    break;
                case BOOLEAN:
                    rowData[j] = cell.getBooleanCellValue();
                    break;
                default:
                    rowData[j] = null;
                    break;
            }
        }
        rows.add(rowData);
    }
    // 构造插入语句
    StringBuilder sqlBuilder = new StringBuilder("INSERT INTO ");
    sqlBuilder.append(tableName).append(" (");
    for (int i = 0; i < columns.length; i++) {
        if (i > 0) {
            sqlBuilder.append(", ");
        }
        sqlBuilder.append(headerRow.getCell(columns[i]).getStringCellValue());
    }
    sqlBuilder.append(") VALUES (");
    for (int i = 0; i < columns.length; i++) {
        if (i > 0) {
            sqlBuilder.append(", ");
        }
        sqlBuilder.append("?");
    }
    sqlBuilder.append(")");
    String sql = sqlBuilder.toString();
    int[] updateCounts;
    try (Connection connection = jdbcTemplate.getDataSource().getConnection();
         PreparedStatement ps = connection.prepareStatement(sql)) {
        // 关闭自动提交事务，保证插入操作的原子性
        connection.setAutoCommit(false);
        // 批量插入数据
        for (Object[] row : rows) {
            for (int i = 0; i < row.length; i++) {
                ps.setObject(i + 1, row[i]);
            }
            ps.addBatch();
        }
        updateCounts = ps.executeBatch();
        // 提交事务
        connection.commit();
    } catch (Exception e) {
        System.out.println(e.fillInStackTrace());
        return false;
    }
    // 检查是否所有数据都成功插入
    return Arrays.stream(updateCounts).allMatch(count -> count == 1);
}
~~~

使用了 POI 库来读取 Excel 文件：

- `StreamingReader.builder().open(inputStream)`：打开 Excel 文件的输入流，并返回一个 `Workbook` 对象。使用 `StreamingReader` 的 `builder()` 方法创建一个 `StreamingReader.Builder` 对象，然后调用 `open()` 方法打开 Excel 文件的输入流，并返回一个 `Workbook` 对象。`StreamingReader` 是 POI 的扩展库之一，可以流式读取 Excel 文件，避免将整个文件加载到内存中。
- `workbook.getSheetAt(0)`：获取 Excel 文件中的第一个工作表（Sheet）对象。
- `sheet.getRow(0)`：获取工作表中的第一行（也就是标题行）对象。
- `headerRow.getLastCellNum()`：获取标题行中单元格的数量（也就是列数）。
- `row.getCell(j)`：获取当前行（除了标题行）中的第 j 个单元格对象。
- `DataFormatter.formatCellValue(cell)`：将单元格的值格式化为字符串类型，避免了大量的类型判断。
- `StringJoiner`：用于将多个字符串拼接成一个字符串，避免频繁的字符串拼接操作。 
- 用于将多个字符串拼接成一个字符串。 通过使用上述函数，我们可以方便地读取 Excel 文件中的数据，并将其插入到 MySQL 数据库中。