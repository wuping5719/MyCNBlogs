<h2>《Spring In Action》 :books: </h2> 

> [美] Craig Walls / Ryan Breidenbach  著    人民邮电出版社

```java
31.产生 Excel 工作表。
  Spring 提供了 org.springframework.web.servlet.view.document.AbstractExcelView，
一个用于在 Spring MVC 中产生 Excel 工作表视图的抽象视图实现。
public class CourseListExcelView extends AbstractExcelView {
  protected void buildExcelDocument(Map model, HSSFWorkbook wb,
      HttpServletRequest request, HttpServletResponse response) throws Exception {
    Set courses = (Set) model.get("courses");
    HSSFSheet sheet = wb.createSheet("Courses");
    HSSFRow header = sheet.createRow(0);
    header.createCell((short)0).setCellValue("ID");
    header.createCell((short)1).setCellValue("Name");
    header.createCell((short)2).setCellValue("Instructor");
    header.createCell((short)3).setCellValue("Start Date");
    header.createCell((short)4).setCellValue("End Date");
    header.createCell((short)5).setCellValue("Students");
    HSSFCellStyle cellStyle = wb.createCellStyle();
    cellStyle.setDataFormat(HSSFDataFormat.getBuiltinFormat("yy-m-d h:mm"));
    int rowNum = 1;
    for (Iterator iter = courses.iterator(); iter.hasNext();) {
      Course course = (Course) iter.next();
      HSSFRow row = sheet.createRow(rowNum++);
      row.createCell((short)0).setCellValue(course.getId().toString());
      row.createCell((short)1).setCellValue(course.getName());
      row.createCell((short)2).setCellValue(course.getInstructor().getLastName());
      row.createCell((short)3).setCellValue(course.getStartDate());
      row.getCell((short)3).setCellStyle(cellStyle);
      row.createCell((short)4).setCellValue(course.getEndDate());
      row.getCell((short)4).setCellStyle(cellStyle);
      row.createCell((short)5).setCellValue(course.getStudents().size());
    }
    HSSFRow row = sheet.createRow(rowNum);
    row.createCell((short)0).setCellValue("TOTAL:");
    String formula = "SUM(F2:F"+rowNum+")";
    row.createCell((short)5).setCellFormula(formula);
  }
}

 response.setContentType("application/vnd.ms-excel");

 <bean id="courseList" class="com.springinaction.training.mvc.CourseListExcelView"/>

 courseList.class=com.springinaction.training.mvc.CourseListExcelView

32.产生 PDF 文档。
  Spring 的 org.springframework.web.servlet.view.document.AbstractPdfView 是一个视图的抽象实现类，
支持作为 Spring MVC 中的视图创建 PDF 文档。
public class CourseListPdfView extends AbstractPdfView {
  protected void buildPdfDocument(Map model, Document pdfDoc, PdfWriter writer, 
      HttpServletRequest request, HttpServletResponse response) throws Exception {
    Set courseList = (Set) model.get("courses");
    Table courseTable = new Table(5);
    courseTable.setWidth(90);
    courseTable.setBorderWidth(1);
    courseTable.addCell("ID");
    courseTable.addCell("Name");
    courseTable.addCell("Instructor");
    courseTable.addCell("Start Date");
    courseTable.addCell("EndDate");
    for (Iterator iter = courseList.iterator(); iter.hasNext();) {
      Course course = (Course) iter.next();
      courseTable.addCell(course.getId().toString());
      courseTable.addCell(course.getName());
      courseTable.addCell(course.getInstructor().getLastName());
      courseTable.addCell(course.getStartDate().toString());
      courseTable.addCell(course.getEndDate().toString());
    }
    pdfDoc.add(courseTable);
  }
}

 <bean id="courseList" class="com.springinaction.training.mvc.CourseListPdfView"/>

 courseList.class=com.springinaction.training.mvc.CourseListPdfView

 改变页面尺寸:
 protected Document getDocument() {
    return new Document(PageSize.LEGAL.rotate());
 }

33.产生其他非 HTML 文件。
 void render(Map model, HttpServletRequest request, HttpServletResponse response) throws Exception;

 一个渲染 JPEG 图像的抽象视图:
 public abstract class AbstractJpegView implements View {
    public AbstractJpegView() {}
    public int getImageWidth() { return 100; }
    public int getImageHeight() { return 100; }
    protected int getImageType() {
      return BufferedImage.TYPE_INT_RGB;
    }
    public void render(Map model, HttpServletRequest request,
        HttpServletResponse response) throws Exception {
      response.setContentType("image/jpeg");
      BufferedImage image = new BufferedImage(getImageWidth(), getImageHeight(), getImageType());
      buildImage(model, image, request, response);
      ServletOutputStream out = response.getOutputStream();
      JPEGImageEncoder encoder = new JPEGImageEncoderImpl(out);
      encoder.encode(image);
      out.flush();
    }
    protected abstract void buildImage(Map model, BufferedImage image,
         HttpServletRequest request, HttpServletResponse response) throws Exception;
  }

  一个绘制圆形的视图:
  public class CircleJpegView extends AbstractJpegView {
    public CircleJpegView() {}
    protected void buildImage(Map model, BufferedImage image,
        HttpServletRequest request, HttpServletResponse response) throws Exception {
      Graphics g = image.getGraphics();
      g.drawOval(0,0,getImageWidth(), getImageHeight());
    }
  }
```
