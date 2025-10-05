# 1. Docker Network
# 2. Docker Compose


# DataBase restore

🐳 Container name → local_pg

🗄️ Database → sanisidro

👤 Username → sanisidro

🔐 Password → sanisidro

🌐 Port → 5432

   Step 0:  start Docker Desktop installation

🧱 Step 1 — Run Postgres container
docker run -d --name local_pg \
  --network estreya-net \
  -e POSTGRES_USER=sanisidro \
  -e POSTGRES_PASSWORD=sanisidro \
  -e POSTGRES_DB=sanisidro \
  -p 5432:5432 \
  postgres:15

🧱 Step 2 — Copy and restore SQL file in one line
docker cp "D:\Office\estreya\San-isidro\estreya_databases_backup_20250930.sql" local_pg:/tmp/backup.sql
docker exec -i local_pg psql -U sanisidro -d sanisidro -f /tmp/backup.sql

🧱 Step 3 - Verify on pgAdmin --> create server and conn










# BACKEND

./mvnw.cmd clean package -DskipTests    //it creates build 

java -jar ./target/sanisidro-0.0.1-SNAPSHOT.jar  

java "-Duser.timezone=Asia/Kolkata" -jar target/sanisidro-0.0.1-SNAPSHOT.jar       //to override the timezone property

# Dockerize Backend 

create Dockerfile (multistage Dockerfile)
use maven in backend dockerfile for builder instead of ./mvnw

docker build -t estreya-backend .          --->  this will create image for backend 

<!-- docker run -d --name local_backend -p 8083:8083 -e SPRING_DATASOURCE_PASSWORD=sanisidro estreya-backend -->

docker run -d --name local_backend \
  --network estreya-net \
  -p 8083:8083 \
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://local_pg:5432/sanisidro \
  -e SPRING_DATASOURCE_USERNAME=sanisidro \
  -e SPRING_DATASOURCE_PASSWORD=sanisidro \
  estreya-backend

# Backend docker done 










# now dockerize FRONTEND 

npm config set strict-ssl false
npm install
npm run dev

# create docker file 
docker build --build-arg NEXT_PUBLIC_API_URL=http://local_backend:8083 -t estreya-frontend .
docker run -d --name local_frontend --network estreya-net -p 8088:8088 estreya-frontend