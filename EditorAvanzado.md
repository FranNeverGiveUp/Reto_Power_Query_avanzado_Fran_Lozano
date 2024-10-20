let
    Origen = "", //Al crear una nueva tabla vacía, siempre se parte desde "Origen.

    Personalizado1 = Table.Combine({ //Table.Combine, añadimos "por abajo" a cada una de las tablas que forman parte de una lista de tablas el contenido de la tabla siguiente.

        Table.AddColumn(Web.Page(Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023/enero")), "Mes", each "1"),
        //Con Table.AddColumn añadimos una columna y con "each" fijamos su valor en lugar de referenciar a otras columnas existentes.
        //Web.Contents devuelve el contenido de una web en HTML.
        //Web.Page traduce el contendio en HTML a estructura de tabla.
        Table.AddColumn(Web.Page(Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023/febrero")), "Mes", each "2"),
        Table.AddColumn(Web.Page(Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023/marzo")), "Mes", each "3"),
        Table.AddColumn(Web.Page(Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023/abril")), "Mes", each "4"),
        Table.AddColumn(Web.Page(Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023/mayo")), "Mes", each "5"),
        Table.AddColumn(Web.Page(Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023/junio")), "Mes", each "6"),
        Table.AddColumn(Web.Page(Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023/julio")), "Mes", each "7"),
        Table.AddColumn(Web.Page(Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023/agosto")), "Mes", each "8"),
        Table.AddColumn(Web.Page(Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023/septiembre")), "Mes", each "9"),
        Table.AddColumn(Web.Page(Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023/octubre")), "Mes", each "10"),
        Table.AddColumn(Web.Page(Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023/noviembre")), "Mes", each "11"),
        Table.AddColumn(Web.Page(Web.Contents("https://www.cuandoenelmundo.com/calendario/espana/2023/diciembre")), "Mes", each "12")
        }),
        
    #"Columnas quitadas" = Table.RemoveColumns(Personalizado1,{"Source", "ClassName", "Id"}), //Elimina las columnas innecesarias.  
    #"Se expandió Data" = Table.ExpandTableColumn(#"Columnas quitadas", "Data", {"Column2", "Column4"}, {"Data.Column2", "Data.Column4"}),
    //Table.ExpandTableColumn expande todas las filas resultado de la combinación de tablas de Table.Combine.
    #"Filtrado sólo días" = Table.SelectRows(#"Se expandió Data", each [Caption] = "España"),
    //Devuelve una tabla con las filas de la tabla que coinciden con la condición elegida.
    #"Columnas quitadas2" = Table.RemoveColumns(#"Filtrado sólo días",{"Caption"}), //Elimina las columnas innecesarias.  

    #"Columnas renombradas" = Table.RenameColumns(#"Columnas quitadas2", {{"Data.Column2", "Día"}, {"Data.Column4", "Festividad"}}),
    #"Columnas añadidas" = Table.AddColumn(#"Columnas renombradas", "Año", each "2023"),
    #"Columnas añadidas2" = Table.CombineColumns(#"Columnas añadidas", {"Día", "Mes", "Año"}, Combiner.CombineTextByDelimiter("/"),"Fecha"),//Combina el contenido de varias columnas en una nueva columna y elimina las columnas originales.

    #"Cambio tipo columna" = Table.TransformColumnTypes(#"Columnas añadidas2",{{"Fecha", type date}, {"Festividad", type text}})

in
    #"Cambio tipo columna"
