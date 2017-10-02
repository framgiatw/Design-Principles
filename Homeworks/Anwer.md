Bài I:
1. Single Respnosibility principle

class AuthenticateAccount
  def authenticate(email, password)
    user = find_from_db(email)
    if user.encrypted_password == encrypt(password)
      # your code here
    else
      # your code here
    end
  end
end
# Class AuthenticateAccount đã thực hiện nhiều nhiệm vụ: Lấy dữ liệu từ db, validate password
# Refactored
class AuthenticateAccount
  def authenticate(email, password)
    if ValidatePasswords.new(email, password).validate?
      # your code here
    else
      # your code here
    end
  end
end

class ValidatePasswords
  def initialize(email, password)
     @email = email
     @password = password
  end

  def validate?
    user = find_from_db(@email)
    user.encrypted_password == encrypt(@password)
  end
end


2. Open/Close principle

class Dog
  def speak
    return "Go go"
  end
end

class Cat
  def speak
    return "Meo Meo"
  end
end

class ZooManager
  def check_speak animal
    if animal.class == "Dog"
      return "Go go"
    elsif animal.class == "Cat"
      return "Meo Meo"
    else
      return ""
    end
  end
end

# Khó cho việc mở rộng thêm các class
# Refactored
module Animal
  def speak
    return ""
  end
end

class Dog < Animal
  def speak
    return "Go go"
  end
end

class Cat < Animal
  def speak
    return "Meo Meo"
  end
end

class ZooManager
  def check_speak animal
    return animal.speak
  end
end

3. Liskov substitution principle
class Bird
  def fly
    return "Flying"
  end
end

class Duck # Vit
  def fly
    return "Duck flying"
  end
end

class Eagle # Diều hâu
  def fly
    return "Eagle flying"
  end
end

class Penguins < Bird #Chim cánh cụt
  def fly
    begin
      # you code here
    rescue => e
      # Exception
    end
  end
end

@bird = [Duck.new(), Eagle.new(), Penguins.new()]
@bird.each{ bird | bird.fly}

# Lỗi Exception bắn ra do chim cánh cụt không thể bay.

4. Interface segregation principle

module Car
  def start_engine
  end

  def stop_engine
  end

  def change_engine
  end
end

class Driver < Car
  def driver
    def start_engine
      # your code here
    end
    def stop_engine
      # your code here
    end
  end
end

class Mechanic
  def mechanic
    def change_engine
      #your code here
    end
  end
end

# Class Driver không cần gánh thêm def change_engine, tương tự cho class Mechanic
#Refactored
module Car
  def start_engine
  end

  def stop_engine
  end
end

module CarInternals
  def change_engine
  end
end

class Driver < Car
  def driver
    def start_engine
      # your code here
    end
    def stop_engine
      # your code here
    end
  end
end

class Mechanic < CarInternals
  def mechanic
    def change_engine
      #your code here
    end
  end
end

5. Dependency inversion principle

class Student
  def update_data
    Database db = Database.new();
    student = db.get_data();
    # your code here
    student = db.set_data();
  end
end

class Databae
  def get_data()
    # your code here
  end
  def set_data()
    # your code here
  end
end

# Class Stusdent đang phụ thuộc vào class Database.
# Nếu class Database thay đổi phương thức save thì class Student cũng phải thay đổi theo.
# Giải pháp sử dụng Module IDatabase ở giữa, class Database và Student làm việc với nhau thông qua IDatabase.
# Refactored
module IDatabase
  def get_data
    # your code here
  end
  def set_data
    # your code here
  end
end

class Database < IDatabase
  def get_data
    # your code here
  end
  def set_data
    # your code here
  end
end

class Student < IDatabase
  def update_data
    Database db = IDatabase.new();
    student = db.get_data();
    # your code here
    student = db.set_data();
  end
end

Bài II: Framgia Crb

Bài toán: Yêu cầu validate name khi signup. (Error syntag + duplicate in database)
Hoàn toàn có thể validate name trong class User trưóc khi create User.
Nhưng để đảm bảo Single Respnosibility:
Coder đã viết riêng class Persion để validate name chứ không viết gộp trong class User.
Cụ thể:
[Persion Class](https://github.com/framgia/framgia_crb_v2/blob/develop/app/models/person.rb)
