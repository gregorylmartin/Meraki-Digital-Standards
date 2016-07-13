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
	) WITH (
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
	) WITH (
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
	) WITH (
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
	) WITH (
		PAD_INDEX = OFF, 
		STATISTICS_NORECOMPUTE = OFF, 
		IGNORE_DUP_KEY = OFF, 
		ALLOW_ROW_LOCKS = ON, 
		ALLOW_PAGE_LOCKS = ON
		) ON [PRIMARY]
	) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
END
GO


SET QUOTED_IDENTIFIER OFF
GO
SET ANSI_PADDING OFF
GO


IF EXISTS ( 
		SELECT  *
    	FROM    sys.objects
    	WHERE   object_id = OBJECT_ID(N'[dbo].[fnAdminFields]')
    	AND type IN (N'FN', N'IF', N'TF', N'FS', N'FT') 
    ) 
    DROP FUNCTION [dbo].[fnAdminFields] ;
GO

PRINT 'Creating Function fnAdminFields...'
GO
CREATE FUNCTION [dbo].[fnAdminFields](     
	@id int = 0
) returns table
as 
return (
	select 
		a.table_id, a.table_name, a.display_name as table_display_name,
		a.table_key, a.show_key, a.where_clause,
		f.field_id, f.field_name, f.display_name as field_display_name,
		dt.data_type_name, it.input_type_name,
		f.lookup_sql 
	from 
	tbl_admin_tables a
	inner join tbl_admin_tables_fields f
		on a.table_id = f.table_id
	inner join tbl_admin_data_types dt
		on f.data_type = dt.data_type
	inner join tbl_admin_input_types it
		on f.input_type = it.input_type 
	where (a.table_id = @id or @id = 0)
)
GO
```

##PHP Controller File
```php
// table.php
<?php
defined('BASEPATH') OR exit('No direct script access allowed');
require(APPPATH . 'core/MY_Basecontroller.php');

class table extends MY_Basecontroller {
    public function __construct() {
        parent::__construct();
        if ($this->data['UserInfo'][0]->Admin != 'Y') {
            echo 'Not Authorized for Admin Functions';
            exit();
        }
    }

    function index($tableId = 0) {
        if ($tableId == 0) {
            echo 'TableID Cannot be 0 !';
            exit();
        } else {
            $this->load->model('mdl_table');
            $this->data['hdr'] = $this->mdl_table->getTableHeader($tableId);
            $this->data['dtl'] = $this->mdl_table->getTableDetail($tableId);
            $this->data['id'] = $tableId;
            $this->load->view('table/table', $this->data);
        }
    }

    function getTableDataOnly($id) {
        $this->load->model('mdl_table');
        $this->data['hdr'] = $this->mdl_table->getTableHeader($id);
        $this->data['dtl'] = $this->mdl_table->getTableDetail($id);
        $this->data['data'] = $this->mdl_table->getTableData($this->data['hdr'], $this->data['dtl']);
        echo json_encode($this->data);
    }

    function tableUpdate($col, $tblId) {
        $this->load->model('mdl_table');
        $this->data['hdr'] = $this->mdl_table->getTableHeader($tblId);
        $this->data['dtl'] = $this->mdl_table->getTableDetail($tblId);
        $id = $_POST['pk'];
        $value = $_POST['value'];
        $this->mdl_table->updateTable($this->data['hdr'], $this->data['dtl'], $value, $id, $col);
    }
}
```

##PHP Model File
```php
<?php
// mdl_table.php
class mdl_table extends CI_Model {
    function getTableHeader($tableId) {
        $this->db = $this->load->database($this->data['Mode_Info'][0]->db_array,TRUE);
        $sql = "select * from tbl_admin_tables where table_id = " . $tableId;
        $query = $this->db->query($sql);
        return $query->result();
    }

    function getTableDetail($tableId) {
        $this->db = $this->load->database($this->data['Mode_Info'][0]->db_array,TRUE);
        $sql = "select * from dbo.fnAdminFields(" . $tableId . ")";
        $query = $this->db->query($sql);
        return $query->result();
    }

    function getTableData($hdr, $dtl) {
        $this->db = $this->load->database($this->data['Mode_Info'][0]->db_array,TRUE);
        $sql = "select " . $hdr[0]->table_key . " as id";
        foreach ($dtl as $row) {
            $sql .= ", " . $row->field_name;
        }
        $sql .=" from " . $hdr[0]->table_name;
        if ($hdr[0]->where_clause != NULL) {
            $sql .=" where " . $hdr[0]->where_clause;
        }

        $query = $this->db->query($sql);
        return $query->result();
    }

    function updateTable($hdr, $dtl, $value, $id, $col) {
        $this->db = $this->load->database($this->data['Mode_Info'][0]->db_array,TRUE);
        $sql = "Update " . $hdr[0]->table_name;
        $sql .= " Set " . $dtl[$col]->field_name . " = ";
        if ($dtl[$col]->data_type_name = 'text') {
            $sql .= '\'' . $value . '\'';
        } else {
            $sql .= $value;
        }
        $sql .= " where " . $hdr[0]->table_key . " = '" . $id . "' ";
        $this->db->query($sql);
    }
}
```

## PHP View File
```php
// table.php
<?php $this->load->view('header') ?>
<link rel="stylesheet" type="text/css" href="<?php echo base_url(); ?>theme/assets/global/plugins/datatables/plugins/bootstrap/dataTables.bootstrap.css"/>
<!-- BEGIN PAGE CONTAINER -->
<div class="page-container">
    <!-- BEGIN PAGE HEAD -->
    <!-- END PAGE HEAD -->
    <!-- BEGIN PAGE CONTENT -->
    <div class="page-content">
        <div class="container-fluid">
            <!-- BEGIN PAGE BREADCRUMB -->
            <div class="row margin-top-5">

                <div class="col-md-12 col-sm-12">
                    <!-- BEGIN PORTLET-->
                    <div class="portlet light ">
                        <div class="portlet-title">
                            <div class="caption caption-md">
                                <i class="icon-bar-chart theme-font hide"></i>
                                <span class="caption-subject theme-font bold uppercase"><?=$hdr[0]->display_name ?></span>
                                <a href="javascript:;" class="reload" data-original-title="" title="">
                                </a>
                            </div>

                            <div class="actions">
                                <div class="btn-group btn-group-devided" data-toggle="buttons">

                                </div>
                            </div>
                        </div>
                        <div class="portlet-body" >
                            <div class="row margin-top-0">
                                <table class="table table-condensed" id="genericTable">
                                    <thead>
                                        <?php foreach ($dtl as $row){ ?>
                                            <th><?=$row->field_display_name?></th>
                                        <?php } ?>
                                    </thead>
                                </table>
                            </div>
                        </div>
                        <div id="loading" class="portlet light" style="display:none">
                            <div class="portlet-body">
                                <center><img src="<?php echo base_url(); ?>theme/img/ajax-loader-large.gif"></center>
                            </div>
                        </div>
                    </div>
                    <!-- END PORTLET-->
                </div>
            </div>
            <!-- END PAGE BREADCRUMB -->
            <!-- BEGIN PAGE CONTENT INNER -->
            <!-- END PAGE CONTENT INNER -->
        </div>
    </div>
    <!-- END PAGE CONTENT -->
</div>
<!-- END PAGE CONTAINER -->
<!-- BEGIN PRE-FOOTER -->

<!-- END PRE-FOOTER -->
<!-- BEGIN FOOTER -->
<?php $this->load->view('footer') ?>
<script type="text/javascript" src="<?php echo base_url(); ?>theme/assets/global/plugins/datatables/media/js/jquery.dataTables.min.js"></script>
<script type="text/javascript" src="<?php echo base_url(); ?>theme/assets/global/plugins/datatables/plugins/bootstrap/dataTables.bootstrap.js"></script>
<script src="<?php echo base_url(); ?>application/views/table/table.js?ver=<?= rand(1, 999999999); ?>" type="text/javascript"></script>
<!-- END JAVASCRIPTS -->
</body>
<!-- END BODY -->
</html>
<script>
    $(document).ready(getTableData(<?=$id?>));
</script>
```

##JS File
```javascript
function getTableData(id) {
    var loading = '#loading';
    $(loading).show();
    dLog(dsg_site_root + "table/getTableDataOnly/" + id);

    $.ajax({
        url: dsg_site_root + "table/getTableDataOnly/" + id,
        type: 'POST',
        async: true,
        cache: false,
        contentType: false,
        processData: false,
        success: function(data) {
            allData = JSON.parse(data);
            jsData = allData.data;
            jsDtl = allData.dtl;
            jsHdr = allData.hdr;
            var tblData = [];

            var myVal;
            for (var i = 0; i < jsData.length; i++) {
                var table = [];
                for (var k = 1; k <= jsDtl.length; k++) {
                    var input_type_name = jsDtl[k-1].input_type_name;
                    var rendered = false;
                    if (input_type_name === 'pulldownxxxx'){
                        rendered = true;
                        table[k - 1]  = '<select name="table_' + k + '_' + i + '" id="table_' + k + '_' + i + '" class="form-control input-small" onclick="" >';
                        table[k - 1] += '<option value="0E4EC352-74C3-49CE-B99F-517B7628CA5B" selected="">BCCCJA - CSD CS PSS SYS LO CII 611</option>';
                        table[k - 1] += '</select>';
                    }

                    if (!rendered){
                        myVal = getByIndex(jsData[i], k) || "";
                        table[k - 1] = '<a href="#" id="table_' + k + '_' + i + '" data-type="text" data-pk="' + jsData[i].id + '" ';
                        table[k - 1] += 'data-url="' + dsg_site_root + 'table/tableUpdate/' + (k - 1) + '/' + id + '" data-title="id">';
                        table[k - 1] += myVal + '</a> ';
                    }
                }
                tblData.push(table);
            }

            $("#genericTable").DataTable({
                data: tblData,
                deferRender: true,
                drawCallback: function() {
                    dLog('drawCallback');
                    $.fn.editable.defaults.mode = 'inline';
                    $("[id^=table_]").editable({
                        success: function() {
                            $(this).show();
                        }
                    });
                }
            });
            $(loading).hide();
        }
    });
}
```
