from http.server import BaseHTTPRequestHandler, HTTPServer
import urllib.parse

class ShoppingHandler(BaseHTTPRequestHandler):

    def do_GET(self):
        if self.path == "/":
            self.show_home()

        elif self.path.startswith("/search"):
            query = urllib.parse.urlparse(self.path).query
            params = urllib.parse.parse_qs(query)
            product = params.get("product", [""])[0]
            self.show_results(product)

    def show_home(self):
        html = """
        <!DOCTYPE html>
        <html>
        <head>
            <title>Product Search</title>
            <style>
                body { font-family: Arial; background:#f4f6f8; text-align:center; padding:40px; }
                input { padding:10px; width:60%; font-size:16px; }
                button { padding:10px 20px; font-size:16px; cursor:pointer; }
            </style>
        </head>
        <body>
            <h1>Search Any Product</h1>
            <input type="text" id="p" placeholder="Enter product name">
            <br><br>
            <button onclick="search()">Search</button>

            <script>
                function search(){
                    let p = document.getElementById("p").value;
                    if(p === ""){
                        alert("Enter product name");
                        return;
                    }
                    window.location.href = "/search?product=" + encodeURIComponent(p);
                }
            </script>
        </body>
        </html>
        """
        self.respond(html)

    def show_results(self, product):
        product_encoded = urllib.parse.quote(product)

        html = f"""
        <!DOCTYPE html>
        <html>
        <head>
            <title>Search Results</title>
            <style>
                body {{ font-family: Arial; background:#f4f6f8; text-align:center; padding:40px; }}
                a {{ display:inline-block; margin:10px; padding:10px 15px;
                     background:#007bff; color:white; text-decoration:none; border-radius:5px; }}
            </style>
        </head>
        <body>
            <h2>Search results for: {product}</h2>

            <a href="https://www.amazon.in/s?k={product_encoded}" target="_blank">Amazon</a>
            <a href="https://www.flipkart.com/search?q={product_encoded}" target="_blank">Flipkart</a>
            <a href="https://www.myntra.com/{product_encoded}" target="_blank">Myntra</a>
            <a href="https://www.meesho.com/search?q={product_encoded}" target="_blank">Meesho</a>

            <br><br>
            <a href="/">Search Again</a>
        </body>
        </html>
        """
        self.respond(html)

    def respond(self, content):
        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        self.wfile.write(content.encode())


# Server start
server = HTTPServer(("localhost", 8080), ShoppingHandler)
print("Server running at http://localhost:8080")
server.serve_forever()
