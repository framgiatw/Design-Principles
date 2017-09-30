class User < ActiveRecord::Base
  attr_accessor :name, :age, :gender, :type

  has_many :tests

  belongs_to :school
  belongs_to :class

  def initialize(user)
    @user = user
  end

  def get_name
    user.name
  end

  def get_age
    user.age
  end

  def open_class
    Class.create(user_id: self.id) if user.type == 'teacher'
  end

  def add_user_to_school user, school
    user.update_attribute school_id: school.id
    if user.age <= 18
      user.update_attributes type: 'student'
    elsif user.age > 18 && user.age < 25
      user.update_attributes type: 'teaching assistant'
    else
      user.update_attributes type: 'teacher'
    end
  end
end

#  This violate the single responsibility and open as the class User has
#  more than one responsibility: the class is changed base on 4 reasons:
#  - defining relation with another class
#  - fetch user information
#  - Create class if user is a teacher, and have him/herself as class teacher
#  - Add user to school based on their types
# Also, the open closed principle is violated because of the function add_user_to_school.
# Assuming we move it to another class called "Assign user role", if there are more user types come up such as
# secretary, class monitor, there would be more "elsif" and that would make the function hard to maintain

# Solution: Split into smaller class :

class User
  def initialize(user); end
  belongs_to :school
  belongs_to :class
end

class GetUserProfile
  def get_name; end
  def get_age; end
end

class ManageClass
  def create_class user
    Class.create(user_id: user.id)
  end
end

# Open closed:

class AssignBaseRole
  def add_user_to_school user, school
    user.update_attributes school_id: school.id
  end
end


class AssignStudent
end

class AssignTeacher
end


##homework2
## https://github.com/mikeknep/SOLID/blob/master/interface_segregation/good/src/
public class Penguin implements SwimmingCreature, FeatheredCreature {
  String currentLocation;
  int numberOfFeathers;

  public Penguin(int initialFeatherCount) {
      this.numberOfFeathers = initialFeatherCount;
  }

  public void swim() {
      this.currentLocation = "in the water";
  }

  public void molt() {
      this.numberOfFeathers -= 4;
  }
}
## Ơ đây, người ta thay vì dùng một interface lớn thì họ đã chia thành nhiều interface nhỏ với mục đích cụ thể(có thể bơi, có thể bay,lông vũ.. cho các loài chim)

##https://github.com/mikeknep/SOLID/blob/master/dependency_inversion/good/src

#EmailClient.java
public class EmailClient implements Notifier {
    public void alertWeatherConditions(String weatherConditions) {
        if (weatherConditions == "sunny");
            System.out.print("It is sunny");
    }
}
# MobileDevice.java
public class MobileDevice implements Notifier {
    public void alertWeatherConditions(String weatherConditions) {
        if (weatherConditions == "rainy")
            System.out.print("It is rainy");
    }
}
# WeatherTracker.java
public class WeatherTracker {
    String currentConditions;

    public void setCurrentConditions(String weatherDescription) {
        this.currentConditions = weatherDescription;
    }

    public void notify(Notifier notifier) {
        notifier.alertWeatherConditions(currentConditions);
    }
}

## Ơ đây, public void notify(Notifier notifier) hàm lấy gía trị đầu vào là một notifier mà không cần quan tâm đấy là notifier của loại nào, không phụ thuộc vào module cấp thấp hơn, mà phụ thuộc vào abstraction.
