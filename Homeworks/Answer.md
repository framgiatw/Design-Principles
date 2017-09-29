
1.

class CalculateSalary
   def getter
	# do something
   end

   def setter
	# do something
   end

   def calculate_salary salary
	if(salary.role == "manager"){
		result = # do something with manager
	}
	else {
		result = # do something with employee
	}
	result * (salary.rank + salary.working_day)
   end

   def export_to_document
	# do something
   end
end


class EmployeeSalary
   attr_accessor :rank, :role, :working_day

   def initalize *args
	
   end
end

class ManagerSalary
   attr_accessor :rank, :role, :working_day
   def initialize *args

   end
end

# violate S, O in SOLID
Explain:
	Class CalculateSalary co tren 1 ly do de thay doi: export, calculate
	Nếu có thêm loại salary thì sẽ phải sửa hàm calculate của class CalculateSalary




Solution:
	Tao them 1 Class khác rieng cho viec export
	Tao thêm 1 class Salary chung

class Salary
   
end

class ExportSalary
end

class EmployeeSalary < Salary
   attr_accessor :rank, :role, :working_day

   def initalize *args
     @rank = args[0]
     @role = args[1]
     @working_day = args[2]
   end

   def result
   	result = # do something with employee
   end
end

class ManagerSalary < Salary
   attr_accessor :rank, :role, :working_day
   def initialize *args
	 @rank = args[0]
     @role = args[1]
     @working_day = args[2]
   end

   def result
   	result = # do something with manager
   end
end


class Salary
	def initialize salary
		@salary = salary
	end
	def get_result
		@salary.result
	end
end


class ExportSalary
	def export
		# do something to export
	end
end











2.


# Open/closed principle
http://hg.openjdk.java.net/jdk6/jdk6/jdk/file/3d2f9f6b0f09/src/share/classes/java/io/

class InputStream, FileInputStream, FilterInputStream, BufferInputStream thiet ke theo Decorator pattern()
Theo decorator pattern:
	Component: InputStream
	Decorator: FilterInputStream
	ConcreteComponents: AudioInputStream, ByteArrayInputStream, FileInputStream, FilterInputStream, InputStream ....
	ConcreteDecorator: BufferedInputStream, CheckedInputStream, CipherInputStream, DataInputStream

Tao ra Decorator FilterInputStream wrap Component InputStream. Dieu nay giup thuan loi khi can extend Component InputStream ma khong lam thay doi 	    Component.
P/s: Em van chua hieu ro khac nhau run-time va compile-time cua inherit




# Singple response principle
https://github.com/wataridori/chatpp/blob/master/src/js/internals/Advertisement.js
class Advertisement: chi dam nhan 1 nhiem vu ve advertisement, khong dam nhan cac chuc nang khac:
	setUp
	changeRandomAdvertisement
	getAdvertisementText


..... continue