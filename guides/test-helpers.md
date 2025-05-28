# Test Helpers

PhoenixSwagger also includes a testing helper module `PhoenixSwagger.SchemaTest` to conveniently assert that responses
from Phoenix controller actions conform to your swagger schema.

In order to validate the swagger schema, the properties options must be required true in swagger definitions:

```elixir
  properties do
    id(:integer, "User ID")
    name(:string, "User name", required: true)
    email(:string, "Email address", format: :email, required: true)
    inserted_at(:string, "Creation timestamp", format: :datetime)
    updated_at(:string, "Update timestamp", format: :datetime)
  end
```


In your controller test files add the `PhoenixSwagger.SchemaTest` mixin with the path to your swagger spec:

```elixir
defmodule YourApp.UserControllerTest do
  use YourApp.ConnCase, async: true
  use PhoenixSwagger.SchemaTest, "priv/static/swagger.json"
```

Then in each test, the context will contain the swagger_schema, which can be used with
the `validate_resp_schema` function:

```elixir
test "shows chosen resource", %{conn: conn, swagger_schema: schema} do
  user = Repo.insert! struct(User, @valid_attrs)
  response =
    conn
    |> get(user_path(conn, :show, user))
    |> validate_resp_schema(schema, "UserResponse")
    |> json_response(200)
end
```
