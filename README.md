# my-shares
share knowledge

# Find the top N largest files in git
git ls-tree -r -t -l --full-name HEAD | sort -n -k 4 | tail -n 10

# Start go-fastdfs
docker run -p8080:8080 -p8088:8088 siyuwuxin/go-fastdfs
Note: 8080 is the file upload page, 8088 is the web port
