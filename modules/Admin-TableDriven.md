```sql
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

SET ANSI_PADDING ON
GO

IF NOT EXISTS (
	SELECT * FROM INFORMATION_SCHEMA.TABLES 
    WHERE TABLE_NAME = N'tbl_admin_data_types')
BEGIN
	PRINT 'Creating tbl_admin_data_types...'
	CREATE TABLE [dbo].[tbl_admin_data_types](
		[data_type] [int] IDENTITY(1,1) NOT NULL,
		[data_type_name] [varchar](50) NULL,
	 CONSTRAINT [PK_tbl_admin_data_types] PRIMARY KEY CLUSTERED 
	(
		[data_type] ASC
	)WITH (
		PAD_INDEX = OFF, 
		STATISTICS_NORECOMPUTE = OFF, 
		IGNORE_DUP_KEY = OFF, 
		ALLOW_ROW_LOCKS = ON, 
		ALLOW_PAGE_LOCKS = ON
		) ON [PRIMARY]
	) ON [PRIMARY]
END
GO

IF NOT EXISTS (
	SELECT * FROM INFORMATION_SCHEMA.TABLES 
    WHERE TABLE_NAME = N'tbl_admin_input_types')
BEGIN
	PRINT 'Creating tbl_admin_input_types...'
	CREATE TABLE [dbo].[tbl_admin_input_types](
		[input_type] [int] IDENTITY(1,1) NOT NULL,
		[input_type_name] [varchar](50) NULL,
	 CONSTRAINT [PK_tbl_admin_input_types] PRIMARY KEY CLUSTERED 
	(
		[input_type] ASC
	)WITH (
		PAD_INDEX = OFF, 
		STATISTICS_NORECOMPUTE = OFF, 
		IGNORE_DUP_KEY = OFF, 
		ALLOW_ROW_LOCKS = ON, 
		ALLOW_PAGE_LOCKS = ON
		) ON [PRIMARY]
	) ON [PRIMARY]
END
GO

IF NOT EXISTS (
	SELECT * FROM INFORMATION_SCHEMA.TABLES 
    WHERE TABLE_NAME = N'tbl_admin_tables')
BEGIN
	PRINT 'Creating tbl_admin_tables...'
	CREATE TABLE [dbo].[tbl_admin_tables](
		[table_id] [int] IDENTITY(1,1) NOT NULL,
		[table_name] [varchar](100) NOT NULL,
		[display_name] [varchar](100) NOT NULL,
		[table_key] [varchar](50) NOT NULL,
		[show_key] [smallint] NOT NULL CONSTRAINT [DF_tbl_admin_tables_show_key]  DEFAULT ((0)),
		[where_clause] [nvarchar](max) NULL,
	 CONSTRAINT [PK_tbl_admin_tables] PRIMARY KEY CLUSTERED 
	(
		[table_id] ASC
	)WITH (
		PAD_INDEX = OFF, 
		STATISTICS_NORECOMPUTE = OFF, 
		IGNORE_DUP_KEY = OFF, 
		ALLOW_ROW_LOCKS = ON, 
		ALLOW_PAGE_LOCKS = ON
		) ON [PRIMARY]
	) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
END
GO

IF NOT EXISTS (
	SELECT * FROM INFORMATION_SCHEMA.TABLES 
    WHERE TABLE_NAME = N'tbl_admin_tables_fields')
BEGIN
	PRINT 'Creating tbl_admin_tables_fields...'
	CREATE TABLE [dbo].[tbl_admin_tables_fields](
		[field_id] [int] IDENTITY(1,1) NOT NULL,
		[table_id] [int] NOT NULL,
		[field_name] [varchar](50) NOT NULL,
		[display_name] [varchar](50) NULL,
		[data_type] [smallint] NULL,
		[input_type] [smallint] NULL,
		[lookup_sql] [nvarchar](max) NULL,
	 CONSTRAINT [PK_tbl_admin_tables_fields] PRIMARY KEY CLUSTERED 
	(
		[field_id] ASC
	)WITH (
		PAD_INDEX = OFF, 
		STATISTICS_NORECOMPUTE = OFF, 
		IGNORE_DUP_KEY = OFF, 
		ALLOW_ROW_LOCKS = ON, 
		ALLOW_PAGE_LOCKS = ON
		) ON [PRIMARY]
	) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
END
GO

--=========================
SET QUOTED_IDENTIFIER OFF
GO

SET ANSI_PADDING OFF
GO
```
