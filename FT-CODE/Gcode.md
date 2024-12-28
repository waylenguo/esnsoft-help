## 商品

### ER图

@startuml

Title 商品明细

entity ft_cd_gvc {
	商品类目
	* gvcode
	--
	gvname
	pgvcode
	path
	sortpath
}

entity ft_cd_gc {
	商品明细
	* gcode
	--
	gname
	gvcode
	gtrgrpcode
	sgtrgrpcode
	qtyunit
	hscode
}

entity ft_cd_gc_att {
	商品附件
	* docicode
}

entity ft_cd_gc_doc {
	商品图片
	* docicode
}

entity ft_cd_gc_img {
	商品图片子表
	* imgicode
}

entity ft_cd_gc_box {
	装箱规格
	* boxicode
}

entity ft_cd_gc_ctnr {
	装柜规格
	* ctnricode
}

entity ft_cd_gc_cc {
	客户货号
	* ccicode
	--
	cgcode
	ccode
}

entity ft_cd_gc_sc {
	供应商货号
	* scicode
	--
	scgcode
	sccode
}

entity ft_cd_gc_lim {
	使用权限
	* limicode
	--
	bcode
	wcode
}

entity ft_cd_gc_qtcut {
	常用签约单位
	* qtcuticode
}


ft_cd_gc_att }o-down-|| ft_cd_gc : gcode
ft_cd_gc_doc }o--|| ft_cd_gc : gcode
ft_cd_gc_img }o--|| ft_cd_gc_doc : docicode

ft_cd_gc_box }o-right-|| ft_cd_gc : gcode
ft_cd_gc_ctnr }o-down-|| ft_cd_gc_box : boxicode

ft_cd_gc ||--o{ ft_cd_gc_cc : gcode
ft_cd_gc ||--o{ ft_cd_gc_sc : gcode

ft_cd_gc ||--o{ ft_cd_gc_lim : gcode
ft_cd_gc ||--o{ ft_cd_gc_qtcut : gcode

ft_cd_gc }o-right-|| ft_cd_gvc : gvcode
@enduml

@startuml

Title 货号属性组

entity ft_cd_gtr {
	货号属性
	* gtrcode
	--
	gtrname
}

entity ft_cd_gtr_data {
	货号属性明细
	*gtrdicode
	--
	* gtrdcode <<FK>>
	* gtrdname
}

entity ft_cd_gtr_sheet {
	单据货号属性关系
	* gtrsicode
	--
	* gtrcode
	* sheetcode
	corpbcode
}

entity ft_cd_gtr_grp {
	货号属性组
	* gtrgrpcode
	--
	* gtrgrpname
}

entity ft_cd_gtr_grpg {
	货号属性组明细
	* gtrgrpgicode
	--
	* gtrgrpcode <<FK>>
	* gtrcode  <<FK>>
	* fldname
}

ft_cd_gtr ||--o{ ft_cd_gtr_data : gtrcode
ft_cd_gtr }|--|{ ft_cd_gtr_sheet : gtrcode
ft_cd_gtr }|--|{ ft_cd_gtr_grpg : gtrcode

ft_cd_gtr_grp ||--o{ ft_cd_gtr_grpg : gtrgrpcode
@enduml

@startuml

Title 库存属性组

entity ft_cd_sgtr {
	库存属性
	* sgtrcode
	--
	sgtrname
}

entity ft_cd_sgtr_data {
	库存属性明细
	*sgtrdicode
	--
	* sgtrdcode <<FK>>
	* sgtrdname
}

entity ft_cd_sgtr_sheet {
	单据库存属性关系
	* sgtrsicode
	--
	* sgtrcode
	* sheetcode
	corpbcode
}

entity ft_cd_sgtr_grp {
	库存属性组
	* sgtrgrpcode
	--
	* sgtrgrpname
}

entity ft_cd_sgtr_grpg {
	库存属性组明细
	* sgtrgrpgicode
	--
	* sgtrgrpcode <<FK>>
	* sgtrcode  <<FK>>
	* fldname
}

ft_cd_sgtr ||--o{ ft_cd_sgtr_data : sgtrcode
ft_cd_sgtr }|--|{ ft_cd_sgtr_sheet : sgtrcode
ft_cd_sgtr }|--|{ ft_cd_sgtr_grpg : sgtrcode

ft_cd_sgtr_grp ||--o{ ft_cd_sgtr_grpg : sgtrgrpcode
@enduml