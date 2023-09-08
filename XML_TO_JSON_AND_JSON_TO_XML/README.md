# Transforming XML messages into JSON and JSON messages into XML without using the [TIBCO ActiveMatrix BusinessWorks™ Plug-in for REST and JSON](https://docs.tibco.com/products/tibco-activematrix-businessworks-plug-in-for-rest-and-json-2-1-0).

In this "How To", I will illustrate the process of transforming XML messages into JSON and JSON messages into XML. TIBCO offers a plugin that facilitates these transformations, along with other features, and **obtaining a license for its use is the recommended and proper approach**. However, some companies may choose not to acquire this plugin. In such cases, as developers, we must explore alternative solutions for achieving these transformations.

For this example, I will use the following message as a foundation:


~~~xml
<?xml version = "1.0" encoding = "UTF-8"?>
<users>
	<user>
		<id>1</id>
		<name>user01</name>
		<email>user01@email.com</email>
	</user>
	<user>
		<id>2</id>
		<name>user02</name>
		<email>user02@email.com</email>
	</user>
	<user>
		<id>3</id>
		<name>user03</name>
		<email>user03@email.com</email>
	</user>
</users>
~~~


## Let's get started...!

1- Create a new TIBCO BW project; I named it **XML_TO_JSON_AND_JSON_TO_XML**, but you can change it as needed. 

![Image 01](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_01_XML_TO_JSON_AND_JSON_TO_XML.png)

2- Create the folder structure to accommodate the XSD schema. I prefer to set it up as **Shared Resources => Schemas**, but feel free to organize it in a way that suits you best.

![Image 02](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_02_XML_TO_JSON_AND_JSON_TO_XML.png)

3- Within the folder structure created in the previous step, create a new XSD schema. It will serve as the foundation for the messages that will be transformed in this example, I named it as **USER-SCHEMA*. Click on the 'Source' tab and paste the following code, click on the "reparse" and save the project.

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
	 elementFormDefault="qualified"
	 attributeFormDefault="unqualified">
	<xs:complexType name="UserType">
		<xs:sequence>
			<xs:element name="id" type="xs:string"/>
			<xs:element name="name" type="xs:string"/>
			<xs:element name="email" type="xs:string"/>
		</xs:sequence>
	</xs:complexType>
	<xs:element name="user" type="UserType"/>
	<xs:element name="users">
		<xs:complexType>
			<xs:sequence>
				<xs:element ref="user" minOccurs="0" maxOccurs="unbounded"/>
			</xs:sequence>
		</xs:complexType>
	</xs:element>
</xs:schema>
~~~

![Image 03](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_03_XML_TO_JSON_AND_JSON_TO_XML.png)

4- Inside the Shared Resources folder, create a new folder to house the JAVA artifacts. I will name it **Java Artifacts**.

![Image 04](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_04_XML_TO_JSON_AND_JSON_TO_XML.png)

5- Download the JAVA [StAXON Core 1.3](https://repo1.maven.org/maven2/de/odysseus/staxon/staxon/1.3/staxon-1.3.jar) library and place it in the **Java Artifacts** folder created in the previous step.

![Image 05](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_05_XML_TO_JSON_AND_JSON_TO_XML.png)

6- We will now create an AliasLibrary to map the JAVA library added to the project. The AliasLibrary will enable us to import this library into the Java Code activity, which will handle the message transformations.

![Image 06](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_06_XML_TO_JSON_AND_JSON_TO_XML.png)

7- Choose the previously created **AliasLibrary** and go to the Aliases tab. Click on the **Create and add alias** button, then select the **staxon-1.3.jar** JAR file acquired in step 5.

![Image 07](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_07_XML_TO_JSON_AND_JSON_TO_XML.png)

8- Create a folder to store the processes responsible for performing the transformations. I will name this folder **Processes**.

![Image 08](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_08_XML_TO_JSON_AND_JSON_TO_XML.png)

9- Within the **Processes** directory, I will create a new process named **Message Transformation**. This process will handle the transformation of **XML** messages into **JSON** and **JSON** messages into **XML**.

![Image 09](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_09_XML_TO_JSON_AND_JSON_TO_XML.png)

10- In the **Message Transformation** process, insert a **Mapper activity**. In this activity, we will map our **input** XML message.

![Image 10](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_10_XML_TO_JSON_AND_JSON_TO_XML.png)

11- In the **Input Editor** tab, make a reference to the **USER-SCHEMA** schema created in step 2.

![Image 11](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_11_XML_TO_JSON_AND_JSON_TO_XML.png)

12- In the **Input** tab, complete the fields with the information as demonstrated in the example image below.

![Image 12](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_12_XML_TO_JSON_AND_JSON_TO_XML.png)

13- Next, add a **Java Code** activity and rename it as **XML to JSON**.

![Image 13](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_13_XML_TO_JSON_AND_JSON_TO_XML.png)

14- Now, in the **Configuration** tab add the input parameters, which I will name **xmlMessage**, and the output parameter, which I will name **jsonMessage**.

![Image 14](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_14_XML_TO_JSON_AND_JSON_TO_XML.png)

15- Navigate to the **Code** tab, select the **Full Class** option, and beneath the line import java.io.*;, include the following imports.

~~~~java
import java.io.ByteArrayInputStream;
import javax.xml.stream.XMLInputFactory;
import javax.xml.stream.XMLEventReader;
import javax.xml.stream.XMLEventWriter;
import de.odysseus.staxon.json.JsonXMLConfig;
import de.odysseus.staxon.json.JsonXMLConfigBuilder;
import de.odysseus.staxon.json.JsonXMLOutputFactory;
import de.odysseus.staxon.xml.util.PrettyXMLStreamWriter;
~~~~

Click on the Compile button.

![Image 15](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_15_XML_TO_JSON_AND_JSON_TO_XML.png)

16- Now, choose the **Invoke Method Body** option, and after the line **/* Available Variables: DO NOT MODIFY *****/**, insert the following code.

~~~~java
InputStream input = new ByteArrayInputStream(xmlMessage.getBytes("UTF-8"));

JsonXMLConfig config = new JsonXMLConfigBuilder().autoArray(true).autoPrimitive(true).prettyPrint(true).build();
XMLEventReader reader = XMLInputFactory.newInstance().createXMLEventReader(input);

Writer output = new StringWriter();
XMLEventWriter writer = new JsonXMLOutputFactory(config).createXMLEventWriter(output);

writer.add(reader);
     
reader.close();
writer.close();

jsonMessage = output.toString();
~~~~

Click on the Compile button.

![Image 16](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_16_XML_TO_JSON_AND_JSON_TO_XML.png)

17- In the **Input** tab, map the output of the **Mapper activity** to the **xmlMessage** field, encapsulating it within the **render-xml()** function.

![Image 17](https://github.com/jgimenes/TIBCO-Community/blob/master/images/IMAGE_17_XML_TO_JSON_AND_JSON_TO_XML.png)

