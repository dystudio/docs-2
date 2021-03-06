# COPY<a name="ZH-CN_TOPIC_0242370560"></a>

## 功能描述<a name="zh-cn_topic_0237122096_zh-cn_topic_0059778766_s0d743b5d862d4cf1829449f474af6d9c"></a>

通过COPY命令实现在表和文件之间拷贝数据。

COPY FROM从一个文件拷贝数据到一个表，COPY TO把一个表的数据拷贝到一个文件。

## 注意事项<a name="zh-cn_topic_0237122096_zh-cn_topic_0059778766_sc996fd2c14664963bae3e1e0ce655441"></a>

-   执行COPY FROM FILENAME或COPY TO FILENAME语句需要SYSADMIN权限，但默认禁止SYSADMIN用户对数据库配置文件，密钥文件，证书文件和审计日志执行COPY FROM FILENAME或COPY TO FILENAME，以防止SYSADMIN用户越权查看或修改敏感文件。放开这一权限需要通过更改enable\_copy\_server\_files的设定来完成。
-   COPY只能用于表，不能用于视图。
-   对任何要插入数据的表必须有插入权限。
-   如果声明了一个字段列表，COPY将只在文件和表之间拷贝已声明字段的数据。如果表中有任何不在字段列表里的字段，COPY FROM将为那些字段插入缺省值。
-   如果声明了数据源文件，服务器必须可以访问该文件；如果指定了STDIN，数据将在客户前端和服务器之间流动，输入时，表的列与列之间使用TAB键分隔，在新的一行中以反斜杠和句点（\\.）表示输入结束。
-   如果数据文件的任意行包含比预期多或者少的字段，COPY FROM将抛出一个错误。
-   数据的结束可以用一个只包含反斜杠和句点（\\.）的行表示。如果从文件中读取数据，数据结束的标记是不必要的；如果在客户端应用之间拷贝数据，必须要有结束标记。
-   COPY FROM中\\N为空字符串，如果要输入实际数据值\\N ，使用\\\\N。

-   COPY FROM不支持在导入过程中对数据做预处理（比如说表达式运算，填充指定默认值等）。如果需要在导入过程中对数据做预处理，用户需先把数据导入到临时表中，然后执行SQL语句通过运算插入到表中，但此方法会导致I/O膨胀，降低导入性能。
-   COPY FROM在遇到数据格式错误时会回滚事务，但没有足够的错误信息，不方便用户从大量的原始数据中定位错误数据。
-   COPY FROM/TO适合低并发，本地小数据量导入导出。

## 语法格式<a name="zh-cn_topic_0237122096_zh-cn_topic_0059778766_s85a73a9ad894403da754c5d6b3d8210f"></a>

-   从一个文件拷贝数据到一个表。

    ```
    COPY table_name [ ( column_name [, ...] ) ] 
        FROM { 'filename' | STDIN }
        [ [ USING ] DELIMITERS 'delimiters' ]
        [ WITHOUT ESCAPING ]
        [ LOG ERRORS ]
        [ REJECT LIMIT 'limit' ]
        [ WITH ( option [, ...] ) ]
        | copy_option
        | FIXED FORMATTER ( { column_name( offset, length ) } [, ...] ) [ ( option [, ...] ) | copy_option [  ...] ] ];
    ```

    >![](public_sys-resources/icon-note.gif) **说明：**   
    >语法中的FIXED FORMATTER \( \{ column\_name\( offset, length \) \} \[, ...\] \)以及 \[ \( option \[, ...\] \) | copy\_option \[ ...\] \] 可以任意排列组合。  

-   把一个表的数据拷贝到一个文件。

    ```
    COPY table_name [ ( column_name [, ...] ) ]
        TO { 'filename' | STDOUT }
        [ [ USING ] DELIMITERS 'delimiters' ]
        [ WITHOUT ESCAPING ]
        [ WITH ( option [, ...] ) ]
        | copy_option
        | FIXED FORMATTER ( { column_name( offset, length ) } [, ...] ) [ ( option [, ...] ) | copy_option [  ...] ] ];
    
    COPY query
        TO { 'filename' | STDOUT }
        [ WITHOUT ESCAPING ]
        [ WITH ( option [, ...] ) ]
        | copy_option
        | FIXED FORMATTER ( { column_name( offset, length ) } [, ...] ) [ ( option [, ...] ) | copy_option [  ...] ] ];
    ```

    >![](public_sys-resources/icon-note.gif) **说明：**   
    >1.  COPY TO语法形式约束如下：  
    >    \(query\)与\[USING\] DELIMITER不兼容，即若COPY TO的数据来自于一个query的查询结果，那么COPY TO语法不能再指定\[USING\] DELIMITERS语法子句。  
    >2.  对于FIXED FORMATTTER语法后面跟随的copy\_option是以空格进行分隔的。  
    >3.  copy\_option是指COPY原生的参数形式，而option是兼容外表导入的参数形式。  
    >4.  语法中的FIXED FORMATTER \( \{ column\_name\( offset, length \) \} \[, ...\] \)以及 \[ \( option \[, ...\] \) | copy\_option \[ ...\] \] 可以任意排列组合。  

    其中可选参数option子句语法为：

    ```
    FORMAT 'format_name'
    | OIDS [ boolean ]
    | DELIMITER 'delimiter_character'
    | NULL 'null_string'
    | HEADER [ boolean ]
    | FILEHEADER 'header_file_string'
    | FREEZE [ boolean ]
    | QUOTE 'quote_character'
    | ESCAPE 'escape_character'
    | EOL 'newline_character'
    | NOESCAPING [ boolean ]
    | FORCE_QUOTE { ( column_name [, ...] ) | * }
    | FORCE_NOT_NULL ( column_name [, ...] )
    | ENCODING 'encoding_name'
    | IGNORE_EXTRA_DATA [ boolean ]
    | FILL_MISSING_FIELDS [ boolean ]
    | COMPATIBLE_ILLEGAL_CHARS [ boolean ]
    | DATE_FORMAT 'date_format_string'
    | TIME_FORMAT 'time_format_string'
    | TIMESTAMP_FORMAT 'timestamp_format_string'
    | SMALLDATETIME_FORMAT 'smalldatetime_format_string'
    ```

    其中可选参数copy\_option子句语法为：

    ```
    OIDS 
    | NULL 'null_string' 
    | HEADER 
    | FILEHEADER 'header_file_string' 
    | FREEZE 
    | FORCE_NOT_NULL column_name [, ...]
    | FORCE_QUOTE { column_name [, ...] | * }
    | BINARY 
    | CSV 
    | QUOTE [ AS ] 'quote_character' 
    | ESCAPE [ AS ] 'escape_character' 
    | EOL 'newline_character'
    | ENCODING 'encoding_name' 
    | IGNORE_EXTRA_DATA 
    | FILL_MISSING_FIELDS
    | COMPATIBLE_ILLEGAL_CHARS 
    | DATE_FORMAT 'date_format_string' 
    | TIME_FORMAT 'time_format_string' 
    | TIMESTAMP_FORMAT 'timestamp_format_string' 
    | SMALLDATETIME_FORMAT 'smalldatetime_format_string'
    ```


## 参数说明<a name="zh-cn_topic_0237122096_zh-cn_topic_0059778766_sd35c0a2e8c2f4c18837224240e8c4e3e"></a>

-   **query**

    其结果将被拷贝。

    取值范围：一个必须用圆括弧包围的SELECT或VALUES命令。

-   **table\_name**

    表的名称（可以有模式修饰）。

    取值范围：已存在的表名。

-   **column\_name**

    可选的待拷贝字段列表。

    取值范围：如果没有声明字段列表，将使用所有字段。

-   **STDIN**

    声明输入是来自标准输入。

-   **STDOUT**

    声明输出打印到标准输出。

-   **FIXED**

    打开字段固定长度模式。在字段固定长度模式下，不能声明DELIMITER，NULL，CSV选项。指定FIXED类型后，不能再通过option或copy\_option指定BINARY、CSV、TEXT等类型。

    >![](public_sys-resources/icon-note.gif) **说明：**   
    >定长格式定义如下：  
    >1.  每条记录的每个字段长度相同。  
    >2.  长度不足的字段以空格填充，数字类型字段左对齐，字符字段右对齐。  
    >3.  字段和字段之间没有分隔符。  

-   **\[USING\] DELIMITER 'delimiters'**

    在文件中分隔各个字段的字符串，分隔符最大长度不超过10个字节。

    取值范围：不允许包含\\.abcdefghijklmnopqrstuvwxyz0123456789中的任何一个字符。

    缺省值：在文本模式下，缺省是水平制表符，在CSV模式下是一个逗号。

-   **WITHOUT ESCAPING**

    在TEXT格式中，不对'\\'和后面的字符进行转义。

    取值范围：仅支持TEXT格式。

-   **LOG ERRORS**

    若指定，则开启对于COPY FROM语句中数据类型错误的容错机制，相关错误行的错误记录会记录到此库中public.pgxc\_copy\_error\_log表中，备后续查阅。

    取值范围：仅支持导入（即COPY FROM）时指定。

    >![](public_sys-resources/icon-note.gif) **说明：**   
    >此容错选项的使用限制如下：  
    >-   此容错机制仅捕捉COPY FROM过程中数据库主节点上数据解析过程中相关的数据类型错误（DATA\_EXCEPTION）。  
    >-   在每个库第一次使用时COPY FROM容错时，请先行检查public.pgxc\_copy\_error\_log（COPY错误表）是否存在，若不存在请调用copy\_error\_log\_create\(\) 函数创建；若存在，请转移此表数据并删除这张表后，调用copy\_error\_log\_create\(\) 函数创建。更多关于表public.pgxc\_copy\_error\_log的字段信息，请参见[表1](其它函数.md#zh-cn_topic_0237121997_table138318280213)。  
    >-   若在指定了LOG ERRORS的COPY FROM运行时，public.pgxc\_copy\_error\_log不存在（未创建或者已删除）或表定义不符合copy\_error\_log\_create\(\) 中的预设表定义，则会报错。因此请确定此COPY错误表是使用copy\_error\_log\_create\(\) 函数创建的，否则可能导致容错的COPY FROM语句无法正常执行。  
    >-   COPY已有的容错选项（如IGNORE\_EXTRA\_DATA）开启时，对应类型的错误会按照已有的方式处理而不会报出异常，因此错误表也不会有相应数据。  

-   **LOG ERRORS DATA**

    LOG ERRORS DATA和LOG ERRORS的区别:

    1.  LOG ERRORS DATA会填充容错表的rawrecord字段。
    2.  只有supper权限的用户才能使用LOG ERRORS DATA参数选项。

        >![](public_sys-resources/icon-caution.gif) **注意：**   
        >使用**“LOG ERRORS DATA”**时，若错误内容过于复杂可能存在写入容错表失败的风险，导致任务失败。  


-   **REJECT LIMIT  **'**limit'**

    与LOG ERROR选项共同使用，对COPY FROM的容错机制设置数值上限，一旦此COPY FROM语句错误数据超过选项指定条数，则会按照原有机制报错。

    取值范围：正整数（1-INTMAX），'unlimited'（无最大值限制）

    缺省值：若未指定LOG ERRORS，则会报错；若指定LOG ERRORS，则默认为0。

    >![](public_sys-resources/icon-note.gif) **说明：**   
    >如上述LOG ERRORS中描述的容错机制，REJECT LIMIT的计数也是按照执行COPY FROM的数据库主节点上遇到的解析错误数量计算，而不是数据库节点的错误数量。  

-   **FORMATTER**

    在固定长度模式中，定义每一个字段在数据文件中的位置。按照column\(offset,length\)格式定义每一列在数据文件中的位置。

    取值范围：

    -   offset取值不能小于0，以字节为单位。
    -   length取值不能小于0，以字节为单位。

    所有列的总长度和不能大于1GB。

    文件中没有出现的列默认以空值代替。

-   **OPTION \{ option\_name ' value '  \}**

    用于指定兼容外表的各类参数。

    -   FORMAT

        数据源文件的格式。

        取值范围：CSV、TEXT、FIXED、BINARY。

        -   CSV格式的文件，可以有效处理数据列中的换行符，但对一些特殊字符处理有欠缺。
        -   TEXT格式的文件，可以有效处理一些特殊字符，但无法正确处理数据列中的换行符。
        -   FIXED格式的文件，适用于每条数据的数据列都比较固定的数据，长度不足的列会添加空格补齐，过长的列则会自动截断。
        -   BINARY形式的选项会使得所有的数据被存储/读作二进制格式而不是文本。 这比TEXT和CSV格式的要快一些，但是一个BINARY格式文件可移植性比较差。

        缺省值：TEXT

    -   DELIMITER

        指定数据文件行数据的字段分隔符。

        >![](public_sys-resources/icon-note.gif) **说明：**   
        >-   分隔符不能是\\r和\\n。  
        >-   分隔符不能和null参数相同，CSV格式数据的分隔符不能和quote参数相同。  
        >-   TEXT格式数据的分隔符不能包含： 小写字母、数字和特殊字符.\\。  
        >-   数据文件中单行数据长度需<1GB，如果分隔符较长且数据列较多的情况下，会影响导出有效数据的长度。  
        >-   分隔符推荐使用多字符和不可见字符。多字符例如'$^&'；不可见字符例如0x07，0x08，0x1b等。  

        取值范围：支持多字符分隔符，但分隔符不能超过10个字节。

        缺省值：

        -   TEXT格式的默认分隔符是水平制表符（tab）。
        -   CSV格式的默认分隔符为“,”。
        -   FIXED格式没有分隔符。

    -   NULL

        用来指定数据文件中空值的表示。

        取值范围：

        -   null值不能是\\r和\\n，最大为100个字符。
        -   null值不能和分隔符、quote参数相同。

        缺省值：

        -   CSV格式下默认值是一个没有引号的空字符串。
        -   在TEXT格式下默认值是\\N。

    -   HEADER

        指定导出数据文件是否包含标题行，标题行一般用来描述表中每个字段的信息。header只能用于CSV，FIXED格式的文件中。

        在导入数据时，如果header选项为on，则数据文本第一行会被识别为标题行，会忽略此行。如果header为off，而数据文件中第一行会被识别为数据。

        在导出数据时，如果header选项为on，则需要指定fileheader。如果header为off，则导出数据文件不包含标题行。

        取值范围：true/on，false/off。

        缺省值：false

    -   QUOTE

        CSV格式文件下的引号字符。

        缺省值：双引号

        >![](public_sys-resources/icon-note.gif) **说明：**   
        >-   quote参数不能和分隔符、null参数相同。  
        >-   quote参数只能是单字节的字符。  
        >-   推荐不可见字符作为quote，例如0x07，0x08，0x1b等。  

    -   ESCAPE

        CSV格式下，用来指定逃逸字符，逃逸字符只能指定为单字节字符。

        缺省值：双引号。当与quote值相同时，会被替换为'\\0'。

    -   EOL 'newline\_character'

        指定导入导出数据文件换行符样式。

        取值范围：支持多字符换行符，但换行符不能超过10个字节。常见的换行符，如\\r、\\n、\\r\\n（设成0x0D、0x0A、0x0D0A效果是相同的），其他字符或字符串，如$、\#。

        >![](public_sys-resources/icon-note.gif) **说明：**   
        >-   EOL参数只能用于TEXT格式的导入导出，不支持CSV格式和FIXED格式导入。为了兼容原有EOL参数，仍然支持导出CSV格式和FIXED格式时指定EOL参数为0x0D或0x0D0A。  
        >-   EOL参数不能和分隔符、null参数相同。  
        >-   EOL参数不能包含：.abcdefghijklmnopqrstuvwxyz0123456789。  

    -   FORCE\_QUOTE \{ \( column\_name \[, ...\] \) | \* \}

        在CSV COPY TO模式下，强制在每个声明的字段周围对所有非NULL值都使用引号包围。NULL输出不会被引号包围。

        取值范围：已存在的字段。

    -   FORCE\_NOT\_NULL \( column\_name \[, ...\] \)

        在CSV COPY FROM模式下，指定的字段输入不能为空。

        取值范围：已存在的字段。

    -   ENCODING

        指定数据文件的编码格式名称，缺省为当前数据库编码格式。

    -   IGNORE\_EXTRA\_DATA

        若数据源文件比外表定义列数多，是否会忽略对多出的列。该参数只在数据导入过程中使用。

        取值范围：true/on、false/off。

        -   参数为true/on，若数据源文件比外表定义列数多，则忽略行尾多出来的列。
        -   参数为false/off，若数据源文件比外表定义列数多，会显示如下错误信息。

            ```
            extra data after last expected column
            ```

        缺省值：false。

        >![](public_sys-resources/icon-notice.gif) **须知：**   
        >如果行尾换行符丢失，使两行变成一行时，设置此参数为true将导致后一行数据被忽略掉。  

    -   COMPATIBLE\_ILLEGAL\_CHARS

        导入非法字符容错参数。此语法仅对COPY FROM导入有效。

        取值范围：true/on，false/off。

        -   参数为true/on，则导入时遇到非法字符进行容错处理，非法字符转换后入库，不报错，不中断导入。
        -   参数为false/off，导入时遇到非法字符进行报错，中断导入。

        缺省值：false/off

        >![](public_sys-resources/icon-note.gif) **说明：**   
        >导入非法字符容错规则如下：  
        >（1）对于'\\0'，容错后转换为空格；  
        >（2）对于其他非法字符，容错后转换为问号；  
        >（3）若compatible\_illegal\_chars为true/on标识导入时对于非法字符进行容错处理，则若NULL、DELIMITER、QUOTE、ESCAPE设置为空格或问号则会通过如"illegal chars conversion may confuse COPY escape 0x20"等报错信息提示用户修改可能引起混淆的参数以避免导入错误。  

    -   FILL\_MISSING\_FIELDS

        当数据加载时，若数据源文件中一行的最后一个字段缺失的处理方式。

        取值范围：true/on，false/off。

        缺省值：false/off

    -   DATE\_FORMAT

        导入对于DATE类型指定格式。此参数不支持BINARY格式，会报“cannot specify bulkload compatibility options in BINARY mode”错误信息。此参数仅对COPY FROM导入有效。

        取值范围：合法DATE格式。可参考[时间和日期处理函数和操作符](时间和日期处理函数和操作符.md)。

        >![](public_sys-resources/icon-note.gif) **说明：**   
        >对于DATE类型内建为TIMESTAMP类型的数据库，在导入的时候，若需指定格式，可以参考下面的timestamp\_format参数。  

    -   TIME\_FORMAT

        导入对于TIME类型指定格式。此参数不支持BINARY格式，会报“cannot specify bulkload compatibility options in BINARY mode”错误信息。此参数仅对COPY FROM导入有效。

        取值范围：合法TIME格式，不支持时区。可参考[时间和日期处理函数和操作符](时间和日期处理函数和操作符.md)。

    -   TIMESTAMP\_FORMAT

        导入对于TIMESTAMP类型指定格式。此参数不支持BINARY格式，会报“cannot specify bulkload compatibility options in BINARY mode”错误信息。此参数仅对COPY FROM导入有效。

        取值范围：合法TIMESTAMP格式，不支持时区。可参考[时间和日期处理函数和操作符](时间和日期处理函数和操作符.md)。

    -   SMALLDATETIME\_FORMAT

        导入对于SMALLDATETIME类型指定格式。此参数不支持BINARY格式，会报“cannot specify bulkload compatibility options in BINARY mode”错误信息。此参数仅对COPY FROM导入有效。

        取值范围：合法SMALLDATETIME格式。可参考[时间和日期处理函数和操作符](时间和日期处理函数和操作符.md)。


-   **COPY\_OPTION \{ option\_name ' value '  \}**

    用于指定COPY原生的各类参数。

    -   NULL null\_string

        用来指定数据文件中空值的表示。

        >![](public_sys-resources/icon-notice.gif) **须知：**   
        >在使用COPY FROM的时候，任何匹配这个字符串的字符串将被存储为NULL值，所以应该确保指定的字符串和COPY TO相同。  

        取值范围：

        -   null值不能是\\r和\\n，最大为100个字符。
        -   null值不能和分隔符、quote参数相同。

        缺省值：

        -   在TEXT格式下默认值是\\N。
        -   CSV格式下默认值是一个没有引号的空字符串。

    -   HEADER

        指定导出数据文件是否包含标题行，标题行一般用来描述表中每个字段的信息。header只能用于CSV，FIXED格式的文件中。

        在导入数据时，如果header选项为on，则数据文本第一行会被识别为标题行，会忽略此行。如果header为off，而数据文件中第一行会被识别为数据。

        在导出数据时，如果header选项为on，则需要指定fileheader。如果header为off，则导出数据文件不包含标题行。

    -   FILEHEADER

        导出数据时用于定义标题行的文件，一般用来描述每一列的数据信息。

        >![](public_sys-resources/icon-notice.gif) **须知：**   
        >-   仅在header为on或true的情况下有效。  
        >-   fileheader指定的是绝对路径。  
        >-   该文件只能包含一行标题信息，并以换行符结尾，多余的行将被丢弃（标题信息不能包含换行符）。  
        >-   该文件包括换行符在内长度不超过1M。  

    -   FREEZE

        将COPY加载的数据行设置为已经被frozen，就像这些数据行执行过VACUUM FREEZE。

        这是一个初始数据加载的性能选项。仅当以下三个条件同时满足时，数据行会被frozen：

        -   在同一事务中create或truncate这张表之后执行COPY。
        -   当前事务中没有打开的游标。
        -   当前事务中没有原有的快照。

        >![](public_sys-resources/icon-note.gif) **说明：**   
        >COPY完成后，所有其他会话将会立刻看到这些数据。但是这违反了MVCC可见性的一般原则，用户应当了解这样会导致潜在的风险。  

    -   FORCE NOT NULL column\_name \[, ...\]

        在CSV COPY FROM模式下，指定的字段不为空。若输入为空，则将视为长度为0的字符串。

        取值范围：已存在的字段。

    -   FORCE QUOTE \{ column\_name \[, ...\]  | \* \}

        在CSV COPY TO模式下，强制在每个声明的字段周围对所有非NULL值都使用引号包围。NULL输出不会被引号包围。

        取值范围：已存在的字段。

    -   BINARY

        使用二进制格式存储和读取，而不是以文本的方式。在二进制模式下，不能声明DELIMITER，NULL，CSV选项。指定BINARY类型后，不能再通过option或copy\_option指定CSV、FIXED、TEXT等类型。

    -   CSV

        打开逗号分隔变量（CSV）模式。指定CSV类型后，不能再通过option或copy\_option指定BINARY、FIXED、TEXT等类型。

    -   QUOTE \[AS\] 'quote\_character'

        CSV格式文件下的引号字符。

        缺省值：双引号。

        >![](public_sys-resources/icon-note.gif) **说明：**   
        >-   quote参数不能和分隔符、null参数相同。  
        >-   quote参数只能是单字节的字符。  
        >-   推荐不可见字符作为quote，例如0x07，0x08，0x1b等。  

    -   ESCAPE \[AS\] 'escape\_character'

        CSV格式下，用来指定逃逸字符，逃逸字符只能指定为单字节字符。

        默认值为双引号。当与quote值相同时，会被替换为'\\0'。

    -   EOL 'newline\_character'

        指定导入导出数据文件换行符样式。

        取值范围：支持多字符换行符，但换行符不能超过10个字节。常见的换行符，如\\r、\\n、\\r\\n（设成0x0D、0x0A、0x0D0A效果是相同的），其他字符或字符串，如$、\#。

        >![](public_sys-resources/icon-note.gif) **说明：**   
        >-   EOL参数只能用于TEXT格式的导入导出，不支持CSV格式和FIXED格式。为了兼容原有EOL参数，仍然支持导出CSV格式和FIXED格式时指定EOL参数为0x0D或0x0D0A。  
        >-   EOL参数不能和分隔符、null参数相同。  
        >-   EOL参数不能包含：.abcdefghijklmnopqrstuvwxyz0123456789。  

    -   ENCODING 'encoding\_name'

        指定文件编码格式名称。

        取值范围：有效的编码格式。

        缺省值：当前编码格式。

    -   IGNORE\_EXTRA\_DATA

        指定当数据源文件比外表定义列数多时，忽略行尾多出来的列。该参数只在数据导入过程中使用。

        若不使用该参数，在数据源文件比外表定义列数多，会显示如下错误信息。

        ```
        extra data after last expected column
        ```

    -   COMPATIBLE\_ILLEGAL\_CHARS

        指定导入时对非法字符进行容错处理，非法字符转换后入库。不报错，不中断导入。此参数不支持BINARY格式，会报“cannot specify bulkload compatibility options in BINARY mode”错误信息。此参数仅对COPY FROM导入有效。

        若不使用该参数，导入时遇到非法字符进行报错，中断导入。

        >![](public_sys-resources/icon-note.gif) **说明：**   
        >导入非法字符容错规则如下：  
        >（1）对于'\\0'，容错后转换为空格；  
        >（2）对于其他非法字符，容错后转换为问号；  
        >（3）若compatible\_illegal\_chars为true/on标识，导入时对于非法字符进行容错处理，则若NULL、DELIMITER、QUOTE、ESCAPE设置为空格或问号则会通过如"illegal chars conversion may confuse COPY escape 0x20"等报错信息提示用户修改可能引起混淆的参数以避免导入错误。  

    -   FILL\_MISSING\_FIELDS

        当数据加载时，若数据源文件中一行的最后一个字段缺失的处理方式。

        取值范围：true/on，false/off。

        缺省值：false/off。

        >![](public_sys-resources/icon-notice.gif) **须知：**   
        >目前COPY指定此Option实际不会生效，即不会有相应的容错处理效果（不生效）。需要额外注意的是，打开此选项会导致解析器在数据库主节点数据解析阶段（即COPY错误表容错的涵盖范围）忽略此数据问题，而到数据库节点重新报错，从而使得COPY错误表（打开LOG ERRORS REJECT LIMIT）在此选项打开的情况下无法成功捕获这类少列的数据异常。因此请不要指定此选项。  

    -   DATE\_FORMAT 'date\_format\_string'

        导入对于DATE类型指定格式。此参数不支持BINARY格式，会报“cannot specify bulkload compatibility options in BINARY mode”错误信息。此参数仅对COPY FROM导入有效。

        取值范围：合法DATE格式。可参考[时间和日期处理函数和操作符](时间和日期处理函数和操作符.md)

        >![](public_sys-resources/icon-note.gif) **说明：**   
        >对于DATE类型内建为TIMESTAMP类型的数据库，在导入的时候，若需指定格式，可以参考下面的timestamp\_format参数。  

    -   TIME\_FORMAT 'time\_format\_string'

        导入对于TIME类型指定格式。此参数不支持BINARY格式，会报“cannot specify bulkload compatibility options in BINARY mode”错误信息。此参数仅对COPY FROM导入有效。

        取值范围：合法TIME格式，不支持时区。可参考[时间和日期处理函数和操作符](时间和日期处理函数和操作符.md)。

    -   TIMESTAMP\_FORMAT 'timestamp\_format\_string'

        导入对于TIMESTAMP类型指定格式。此参数不支持BINARY格式，会报“cannot specify bulkload compatibility options in BINARY mode”错误信息。此参数仅对COPY FROM导入有效。

        取值范围：合法TIMESTAMP格式，不支持时区。可参考[时间和日期处理函数和操作符](时间和日期处理函数和操作符.md)。

    -   SMALLDATETIME\_FORMAT 'smalldatetime\_format\_string'

        导入对于SMALLDATETIME类型指定格式。此参数不支持BINARY格式，会报“cannot specify bulkload compatibility options in BINARY mode”错误信息。此参数仅对COPY FROM导入有效。

        取值范围：合法SMALLDATETIME格式。可参考[时间和日期处理函数和操作符](时间和日期处理函数和操作符.md)。

    COPY FROM能够识别的特殊反斜杠序列如下所示。

    -   **\\b**：反斜杠 （ASCII 8）
    -   **\\f**：换页（ASCII 12）
    -   **\\n**：换行符 （ASCII 10）
    -   **\\r**：回车符 （ASCII 13）
    -   **\\t**：水平制表符 （ASCII 9）
    -   **\\v**：垂直制表符 （ASCII 11）
    -   **\\digits**：反斜杠后面跟着一到三个八进制数，表示ASCII值为该数的字符。
    -   **\\xdigits**：反斜杠x后面跟着一个或两个十六进制位声明指定数值编码的字符。


## 示例<a name="zh-cn_topic_0237122096_zh-cn_topic_0059778766_s30bb80bf2fbd4cb3af1ab84e7cb1e0c9"></a>

```
--将tpcds.ship_mode中的数据拷贝到/home/omm/ds_ship_mode.dat文件中。
postgres=# COPY tpcds.ship_mode TO '/home/omm/ds_ship_mode.dat';

--将tpcds.ship_mode 输出到stdout。
postgres=# COPY tpcds.ship_mode TO stdout;

--创建tpcds.ship_mode_t1表。
postgres=# CREATE TABLE tpcds.ship_mode_t1
(
    SM_SHIP_MODE_SK           INTEGER               NOT NULL,
    SM_SHIP_MODE_ID           CHAR(16)              NOT NULL,
    SM_TYPE                   CHAR(30)                      ,
    SM_CODE                   CHAR(10)                      ,
    SM_CARRIER                CHAR(20)                      ,
    SM_CONTRACT               CHAR(20)
)
WITH (ORIENTATION = COLUMN,COMPRESSION=MIDDLE)
;

--从stdin拷贝数据到表tpcds.ship_mode_t1。
postgres=# COPY tpcds.ship_mode_t1 FROM stdin;

--从/home/omm/ds_ship_mode.dat文件拷贝数据到表tpcds.ship_mode_t1。
postgres=# COPY tpcds.ship_mode_t1 FROM '/home/omm/ds_ship_mode.dat';

--从/home/omm/ds_ship_mode.dat文件拷贝数据到表tpcds.ship_mode_t1，使用参数如下：导入格式为TEXT（format 'text'），分隔符为'\t'（delimiter E'\t'），忽略多余列（ignore_extra_data 'true'），不指定转义（noescaping 'true'）。
postgres=# COPY tpcds.ship_mode_t1 FROM '/home/omm/ds_ship_mode.dat' WITH(format 'text', delimiter E'\t', ignore_extra_data 'true', noescaping 'true');

--从/home/omm/ds_ship_mode.dat文件拷贝数据到表tpcds.ship_mode_t1，使用参数如下：导入格式为FIXED（FIXED），指定定长格式（FORMATTER(SM_SHIP_MODE_SK(0, 2), SM_SHIP_MODE_ID(2,16), SM_TYPE(18,30), SM_CODE(50,10), SM_CARRIER(61,20), SM_CONTRACT(82,20))），忽略多余列（ignore_extra_data），有数据头（header）。
postgres=# COPY tpcds.ship_mode_t1 FROM '/home/omm/ds_ship_mode.dat' FIXED FORMATTER(SM_SHIP_MODE_SK(0, 2), SM_SHIP_MODE_ID(2,16), SM_TYPE(18,30), SM_CODE(50,10), SM_CARRIER(61,20), SM_CONTRACT(82,20)) header ignore_extra_data;

--删除tpcds.ship_mode_t1。
postgres=# DROP TABLE tpcds.ship_mode_t1;
```

