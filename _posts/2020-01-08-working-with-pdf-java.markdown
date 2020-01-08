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

To use iText we should add the `itext7-core` to our project.

{% highlight xml %}

    <dependency>
        <groupId>com.itextpdf</groupId>
        <artifactId>itext7-core</artifactId>
        <version>7.1.9</version>
        <type>pom</type>
    </dependency>

{% endhighlight %}

Also we can add the dependency for our tests.

{% highlight xml %}

    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.5.2</version>
        <scope>test</scope>
    </dependency>

{% endhighlight %}

# Create a simple paragraph

To create a simple pdf file we need to create a new Document as follows.

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

1. `PdfWriter` is an object that can write a PDF file. We pass as a parameter the path of the File.
2. The `PdfDocument` manages the content that is added, distributes that content over different pages, and keeps track of whatever information is relevant for that content
3. We create a `Document` that takes the `PdfDocument` as parameter. Now that we have the document object, we can forget that we're creating PDF.
4. We create a `Paragraph` containing the text `"Hello! Welcome to iTextPdf"` and we add that paragraph to the document object.
5. We close the `document`.

When running the test we should get a PDF similar to this one:

![Screenshot 01](/assets/20200108itext/screenshot01.jpg)

# Adding a List

To add a List to our document we need to use the `List` object.

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

The `List` object requires some `ListItem` objects to be added. Also we can configure some parameters as the indent, symbol or font. 
For example we can use Times Roman font creating a instance of `PdfFont` class using the `PdfFontFactory` factory class.  

> `FontConstants` class is deprecated, we should use `StandardFonts` instead.


![Screenshot 02](/assets/20200108itext/screenshot02.jpg)

# Adding a simple Table

To add a Table to our document we need to use the `Table` object.

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
Using `UnitValue` and `useAllAvailableWidth()` we can define a table of 8 columns using all available width of the document.

With `addCell` method we can add rows to the table till filling all the available columns.

![Screenshot 03](/assets/20200108itext/screenshot03.jpg)

# Adding a Table with format and background

We can use the classes `Cell` and `PdfFont` in order to define the format of the cell. 

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

We can set the font to a `Paragraph` object and assigning this paragraph to the cell instead of using a String. For the cell style we can define some properties as:
* Background
* Border
* Alignment of the text

![Screenshot 04](/assets/20200108itext/screenshot04.jpg)

# Source Code

```
git clone https://github.com/jocamav/spring-boot-starting
```

# References

[iText examples][itext-examples]

[itext-examples]: https://itextpdf.com/en/resources/books/itext-7-examples/itext-7-examples
