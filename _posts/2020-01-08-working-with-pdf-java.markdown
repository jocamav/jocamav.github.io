---
layout: post
title:  "Building an Application with Spring Boot"
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

# Source Code

```
git clone https://github.com/jocamav/spring-boot-starting
```

# References

[iText examples][itext-examples]


[itext-examples]: https://itextpdf.com/en/resources/books/itext-7-examples/itext-7-examples
