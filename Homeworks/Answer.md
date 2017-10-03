#Homework 1
1. Single Respnosibility principle
class AuthenticatesUser
  def authenticate email, password
    if matches? email, password
      do_some_authentication
    else
      raise NotAllowedError
    end
  end

  private

  def matches? email, password
    user = find_from_db :user, email
    user.encrypted_password == encrypt(password)
  end
end

#Class AuthenticatesUser được sử dụng cho việc xác thực người dùng là kiểm tra xem email và mật khẩu họ nhập vào có khớp với dữ liệu được lưu trong database hay không. Nó có 2 nhiệm vụ (kiểm tra email, mật khẩu; xác thực người dùng), và theo nguyên tắc này thì nó chỉ nên giữ lại 1 nhiệm vụ.

#Refactor
class MatchesPasswords
  def initialize email, password
    @email = email
    @password = password
  end

  def matches?
    user = find_from_db :user, @email
    user.encrypted_password == encrypt(@password)
  end
end

class AuthenticatesUser
  def authenticate email, password
    if MatchesPasswords.new(email, password).matches?
      do_some_authentication
    else
      raise NotAllowedError
    end
  end
end

2. Open/Close principle
class Report
  def body
    generate_reporty_stuff
  end

  def print
    body.to_json
  end
end

#Vi phạm nguyên tắc đóng/mở bởi vì nếu bạn muốn thay đổi định dạng in ra của báo cáo, bạn cần thay đổi trực tiếp mã nguồn của class Report

#Refactor
class Report
  def body
    generate_reporty_stuff
  end

  def print formatter: JSONFormatter.new
    formatter.format body
  end
end

3. Liskov substitution principle
class Animal
  def walk
    do_some_walkin
  end
end

class Cat < Animal
  def run
    run_like_a_cat
  end
end

#Để tuân thủ theo nguyên tắc thay thế Liskov, chúng phải có cùng interface

#Refactor
class Animal
  def walk
    do_some_walkin
  end

  def run
    raise NotImplementedError
  end
end

class Cat < Animal
  def run
    run_like_a_cat
  end
end

4. Interface segregation principle
class Car
  def open
  end

  def start_engine
  end

  def change_engine
  end
end

class Driver
  def drive
    @car.open
    @car.start_engine
  end
end

class Mechanic
  def do_stuff
    @car.change_engine
  end
end

#Class Car có một interface được sử dụng một phần bởi cả 2 class Driver và Mechanic

#Refactor
class Car
  def open
  end

  def start_engine
  end
end

class CarInternals
  def change_engine
  end
end

class Driver
  def drive
    @car.open
    @car.start_engine
  end
end

class Mechanic
  def do_stuff
    @car_internals.change_engine
  end
end

5. Dependency inversion principle
class Report
  def body
    generate_reporty_stuff
  end

  def print
    JSONFormatter.new.format body
  end
end

class JSONFormatter
  def format body
    ...
  end
end

#Method print của class Report đang được fix cứng, từ đó sẽ tạo nên sự phụ thuộc của class này vào class JSONFormatter. Vì Report là class trừu tượng ở mức cao hơn so với JSONFormatter, cách viết này đang vi phạm nguyên tắc dependency inversion

#Refactor
class Report
  def body
    generate_reporty_stuff
  end

  def print formatter: JSONFormatter.new
    formatter.format body
  end
end


#Homework 2
https://github.com/rails/rails/blob/master/activemodel/lib/active_model/secure_password.rb
https://github.com/rails/rails/blob/master/activemodel/lib/active_model/validations.rb

module ActiveModel
  module SecurePassword
    extend ActiveSupport::Concern
    //
    module ClassMethods
      begin
        require "bcrypt"
      rescue LoadError
        $stderr.puts "You don't have bcrypt installed in your application. Please add it to your Gemfile and run bundle install"
        raise
      end

      include InstanceMethodsOnActivation

      if options.fetch(:validations, true)
        include ActiveModel::Validations

        validate do |record|
          record.errors.add(:password, :blank) unless record.password_digest.present?
        end

        validates_length_of :password, maximum: ActiveModel::SecurePassword::MAX_PASSWORD_LENGTH_ALLOWED
        validates_confirmation_of :password, allow_blank: true
      end
    end
  end
end


module ActiveModel
  module Validations
    extend ActiveSupport::Concern
    //
    module ClassMethods
      def validates_each(*attr_names, &block)
        validates_with BlockValidator, _merge_attributes(attr_names), &block
      end
      //
    end
  end
end

#Tuân thủ nguyên tắc Singple response principle, các module SecurePassword và Validations mỗi module chỉ thực hiện một nhiệm vụ. Đối với SecurePassword là để mã hóa chuỗi password, còn Validations là để validate các trường dữ liệu.
