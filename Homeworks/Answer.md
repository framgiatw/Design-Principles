# Framgia Training Workshop - SOLID
## Homework answer (Group 1)

### Homework 1
 Class Student có các hàm lấy thông tin học sinh, tính học phí, format cấu trúc thông tinh học sinh …
```php
class Student
{
    private $infor;
    private $score;
    private $type;
    private $tuitionFee;


    public function getInfor()
    {
        return $this->infor;
    }
    public function getScore()
    {
        return $this->score;
    }

    public function payTuitionFee()
    {
        switch ($this->type) {
            case 'foreign':
                return $this->tuitionFee * 1.5;
                break;

            case 'policy':
                return $this->tuitionFee * 0.75;
                break;

            case 'normal':
                return $this->tuitionFee;
                break;
                
            default:
                return 'not found';
                break;
        }
    }

    public function getContents()
    {
        return [
            'infor' => $this->getInfor(),
            'score' => $this->getScore(),
        ];
    }

    public function formatJson()
    {
        return json_encode($this->getContents());
    }
}
```

Đoạn code trên đã vi phạm 2 nguyên tắc 1 và 2 của SOLID là Single Responsibility principle và Open-Closed principle.

Nguyên tắc 1 vi phạm ở function `formatJson()`, class `Student` chỉ nên làm nhiệm vụ liên quan trực tiếp đến học sinh, việc format dữ liệu không liên quan trực tiếp đến học sinh đó. Nên để việc format dữ liệu cho một class khác đảm nhận, khi thay đổi định dạng format không cần động đến class `Student ` nên hoạt động của class vẫn được đảm bảo.

Nguyên tắc 2 vi phạm ở function `payTuitionFee`, việc tính học phí được dựa theo các đối tượng học sinh khác nhau, khi thêm 1 loại học sinh mới(mở rộng chương trình) thì cần phải sửa đổi code ở fucntion `payTuitionFee`, việc sửa này có thể gây ảnh hưởng đến việc tính học phí ở các loại học sinh có sẵn, hoặc ảnh hưởng đến các hành động khác trong class. Nên tách mỗi loại học sinh ra một đối tượng riêng và cùng extends class Student có các properties và functions chung, còn hành động  `payTuitionFee` sẽ được xử lý riêng ở từng đối tượng thông qua việc implements interface `PayTuitionFee`

#Refactored

```php
class Student
{
    public function getInfor()
    {
        return $this->infor;
    }
    public function getScore()
    {
        return $this->score;
    }

    public function getContents()
    {
        return [
            'infor' => $this->getInfor(),
            'score' => $this->getScore(),
        ];
    }

}

class ForeignStudent extends Student implements PayTuitionFee
{
    public function payTuitionFee()
    {
        return $this->tuitionFee * 1.5;
    }
}

class PolicyStudent extends Student implements PayTuitionFee
{
    public function payTuitionFee()
    {
        return $this->tuitionFee * 075;
    }
}

interface PayTuitionFee
{
    public function payTuitionFee();
}

interface StudentFormattable
{
    public function format();
}

class JsonStudentFormatter implements StudentFormattable
{
    public function format(Student $student)
    {
        return json_encode($student->getContents());
    }
}
```

### Homework 2

Source code https://github.com/ovedfs/laravel-repo2

Project đã áp dụng rất rõ ràng nguyên lý thứ 5 trong SOLID, thông qua việc sử dụng mô hình design `Repository design pattern`

Các controller(module cấp cao) không phụ thuộc trực tiếp vào các class xử lý database(module cấp thấp), thay vào đó nó implements các interface, service container sẽ làm nhiệm vụ tiêm instance của class database(đã được đăng kí với service container) vào controller thông qua tính năng Interface Binding mạnh mẽ của laravel. Vì vậy khi cần thay đổi database ta chỉ cần thay đổi việc đăng kí class với service container
