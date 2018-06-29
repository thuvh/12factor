## III. Cấu hình
### Lưu trữ cấu hình trong môi trường

Cấu hình của ứng dụng là những thứ có thể thay đổi qua các [triển khai](./codebase) (hệ thống thử, hệ thống sản xuất, môi trường phát triển, etc). Nó bao gồm:
* Tài nguyên xử lý cơ sở dữ liệu, Memcached, và [dịch vụ lớp dưới](./backing-services) khác
* Thông tin đăng nhập đến các dịch vụ như là Amazon S3 hay Twitter
* Các giá trị ứng với từng triển khai như như là tên của máy chủ để triển khai

Các ứng dụng thường lưu trữ các cấu hình như là hằng số trong mã nguồn. Điều này không phù hợp với nguyên tắc của 12-thừa số, yêu cầu **giới hạn tách biệt các cấu hình khỏi mã nguồn**. Các cấu hình thay đổi qua các triển khai, mã nguồn thì không.

A litmus test for whether an app has all config correctly factored out of the code is whether the codebase could be made open source at any moment, without compromising any credentials.

Note that this definition of "config" does **not** include internal application config, such as `config/routes.rb` in Rails, or how [code modules are connected](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/beans.html) in [Spring](http://spring.io/).  This type of config does not vary between deploys, and so is best done in the code.

Another approach to config is the use of config files which are not checked into revision control, such as `config/database.yml` in Rails.  This is a huge improvement over using constants which are checked into the code repo, but still has weaknesses: it's easy to mistakenly check in a config file to the repo; there is a tendency for config files to be scattered about in different places and different formats, making it hard to see and manage all the config in one place.  Further, these formats tend to be language- or framework-specific.

**The twelve-factor app stores config in *environment variables*** (often shortened to *env vars* or *env*).  Env vars are easy to change between deploys without changing any code; unlike config files, there is little chance of them being checked into the code repo accidentally; and unlike custom config files, or other config mechanisms such as Java System Properties, they are a language- and OS-agnostic standard.

Another aspect of config management is grouping.  Sometimes apps batch config into named groups (often called "environments") named after specific deploys, such as the `development`, `test`, and `production` environments in Rails.  This method does not scale cleanly: as more deploys of the app are created, new environment names are necessary, such as `staging` or `qa`.  As the project grows further, developers may add their own special environments like `joes-staging`, resulting in a combinatorial explosion of config which makes managing deploys of the app very brittle.

In a twelve-factor app, env vars are granular controls, each fully orthogonal to other env vars.  They are never grouped together as "environments", but instead are independently managed for each deploy.  This is a model that scales up smoothly as the app naturally expands into more deploys over its lifetime.
