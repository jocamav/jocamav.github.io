---
layout: post
title:  "Working with PDF in Java"
date:   2020-01-08 14:38:12 +0100
categories: [java, pdf, itext]
---

# Introduction

iText 7 Core is a commercially licensed PDF library and SDK available for Java or .NET (C#). In the PDF library, developers will find the code to program PDF documents. You can also extend the functionality of  iText 7 Core with iText add-ons.

> When using iText PDF in a closed source environment, you will need to purchase an iText PDF commercial license.

# Technologies used

* JDK 1.8
* Maven 3
* iText 7.1.9
* jUnit 5.5.2

# pom.xml

To use iText we should add the `itext7-core` dependency to our project.

{% highlight xml %}

    <dependency>
        <groupId>com.itextpdf</groupId>
        <artifactId>itext7-core</artifactId>
        <version>7.1.9</version>
        <type>pom</type>
    </dependency>

{% endhighlight %}

For executing the tests we should add also jUnit 5.

{% highlight xml %}

    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.5.2</version>
        <scope>test</scope>
    </dependency>

{% endhighlight %}

# Create a simple paragraph

Let's start creating a simple Hello World PDF File. To create the file we need to create a instance of `com.itextpdf.layout.Document` as follows.

{% highlight java %}
    @Test
    public void generateSimplePdf() throws IOException {
        PdfDocument pdf = new PdfDocument(new PdfWriter("hello world.pdf"));
        Document document = new Document(pdf);
        String line = "Hello! Welcome to iTextPdf";
        document.add(new Paragraph(line));
        document.close();
    }
{% endhighlight %}

Let's take a deeper look to this example:

1. `PdfWriter` is an object that can write a PDF file. We pass as a parameter the path of the File (if the file doesn't exist it will create it).
2. The `PdfDocument` manages the content that is added, distributes that content over different pages, and keeps track of whatever information is relevant for that content
3. We create a `Document` that takes the `PdfDocument` as parameter. Now that we have the document object, we can forget that we're creating PDF.
4. We create a `Paragraph` containing the text `"Hello! Welcome to iTextPdf"` and we add that paragraph to the document object.
5. We close the `document`.

After running the test we should get a PDF file similar to this one:

![Screenshot 01](/assets/20200108itext/screenshot01.jpg)

# Adding a List

iText allows us to manage lists of items as well. To add a List to our document we need to use `com.itextpdf.layout.element.List` class.

{% highlight java %}
    @Test
    public void generateListPdf() throws IOException {
        PdfDocument pdf = new PdfDocument(new PdfWriter("list example.pdf"));
        Document document = new Document(pdf);
        PdfFont font = PdfFontFactory.createFont(StandardFonts.TIMES_ROMAN);

        document.add(new Paragraph("iText is:").setFont(font));

        List list = new List()
                .setSymbolIndent(12)
                .setFont(font);

        list.add(new ListItem("Never gonna give you up"))
                .add(new ListItem("Never gonna let you down"))
                .add(new ListItem("Never gonna run around and desert you"))
                .add(new ListItem("Never gonna make you cry"))
                .add(new ListItem("Never gonna say goodbye"))
                .add(new ListItem("Never gonna tell a lie and hurt you"));

        document.add(list);
        document.close();
    }
{% endhighlight %}

The `List` must be populated with some `ListItem` which contain the content of each item of the list. We can configure some parameters as the indent, symbol or font if needed. 
For this example we'll use Times Roman font. To set the font we need to create an instance of `PdfFont` class using the `PdfFontFactory` factory class.  

> `FontConstants` class is deprecated. We should use `StandardFonts` instead.

The example above should generate a PDF file as follows.

![Screenshot 02](/assets/20200108itext/screenshot02.jpg)

# Adding a simple Table

We can add tables to our document as well. To add a simple table to our document we need to create an instance of `com.itextpdf.layout.element.Table` object.

{% highlight java %}
    @Test
    public void generateTable() throws IOException {
        PdfDocument pdfDoc = new PdfDocument(new PdfWriter("table example.pdf"));
        Document doc = new Document(pdfDoc);

        Table table = new Table(UnitValue.createPercentArray(8)).useAllAvailableWidth();

        for (int i = 0; i < 16; i++)
        {
            table.addCell("hi");
        }

        doc.add(table);
        doc.close();
    }
{% endhighlight %}

A `Table` is a layout element that represents data in a two-dimensional grid. It is filled with cells, ordered in rows and columns.
We can configure the structure and distribution of the table. In this case we create a table of 8 columns using all available width of the document.

With `addCell` method we can add new cells to the table. If a new cell is added when we reach the column limit of the table a new row is created.

![Screenshot 03](/assets/20200108itext/screenshot03.jpg)

# Adding a Table with format and background

The previous example creates cells with the default format. If we want to format the content of a row we can use the class `PdfFont`. 
In this example we'll use the `Helvetica` font with bold letters and white color.

{% highlight java %}
    @Test
    public void tableWithBackGround() throws IOException {
        PdfDocument pdfDoc = new PdfDocument(new PdfWriter("table with Background.pdf"));
        Document doc = new Document(pdfDoc);

        Table table;
        Cell cell;
        PdfFont font = PdfFontFactory.createFont(StandardFonts.HELVETICA_BOLD);
        table = new Table(UnitValue.createPercentArray(16)).useAllAvailableWidth();
        for (int aw = 0; aw < 16; aw++) {
            cell = new Cell().add(new Paragraph("hi").setFont(font).setFontColor(ColorConstants.WHITE));
            cell.setBackgroundColor(ColorConstants.BLUE);
            cell.setBorder(Border.NO_BORDER);
            cell.setTextAlignment(TextAlignment.CENTER);
            table.addCell(cell);
        }
        doc.add(table);

        doc.close();
    }
{% endhighlight %}

The `PdfFont` object must be set to a `Paragraph` object and assigning this paragraph to the cell instead of using a `String`. For the cell style we can define some properties as:
* Background
* Border
* Alignment of the text

The PDF generated should look as this one.

![Screenshot 04](/assets/20200108itext/screenshot04.jpg)


# Adding a Table with custom header

With all this features we can define a table with a custom header and some rows with the default format.

{% highlight java %}
    @Test
    public void tableWithHeaderAndFormat() throws IOException {
        PdfDocument pdfDoc = new PdfDocument(new PdfWriter("table with header.pdf"));
        Document doc = new Document(pdfDoc);

        Table table = new Table(UnitValue.createPercentArray(3)).useAllAvailableWidth();

        addRowHeaderToTable(table);
        addRowsToTable(table, 5);
        doc.add(table);

        doc.close();
    }
{% endhighlight %}

Here it is the method to generate the Header row:

{% highlight java %}
    private void addRowHeaderToTable(Table table) throws IOException {
        PdfFont font = PdfFontFactory.createFont(StandardFonts.HELVETICA_BOLD);
        Stream.of("column header 1", "column header 2", "column header 3")
                .forEach(columnTitle -> {
                    Cell header = new Cell();
                    header.add(new Paragraph(columnTitle).setFont(font).setFontColor(ColorConstants.WHITE));
                    header.setBackgroundColor(ColorConstants.LIGHT_GRAY);
                    header.setBorder(new SolidBorder(ColorConstants.BLUE, 1));
                    table.addCell(header);
                });
    }
{% endhighlight %}

And also the method to generate some mock data for the rows.

{% highlight java %}
    private void addRowsToTable(Table table, int numberOfRows) {
        for (int i = 0; i < numberOfRows; i++) {
            for (int j = 0; j < table.getNumberOfColumns(); j++) {
                table.addCell(String.format("Row [%d] Column [%d]", i + 1, j + 1));
            }
        }
    }
{% endhighlight %}

Creating a table with a header with grey brackground, blue thick border and white letters. 

![Screenshot 05](/assets/20200108itext/screenshot05.jpg)

# Source Code

![Github Logo][github-logo]
[GitHub Project](https://github.com/jocamav/tutorials/tree/master/itext-pdf)

```
git clone https://github.com/jocamav/tutorials.git
cd itext-pdf
mvn test
```

# References

[iText examples][itext-examples]

[itext-examples]: https://itextpdf.com/en/resources/books/itext-7-examples/itext-7-examples
[github-logo]: /assets/logos/github-mark-32.png
