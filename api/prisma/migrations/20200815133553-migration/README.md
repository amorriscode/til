# Migration `20200815133553-migration`

This migration has been generated by Alice Zhao at 8/15/2020, 1:35:53 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
CREATE TYPE "LearningType" AS ENUM ('SUCCESS', 'FAILURE');

CREATE TABLE "public"."User" (
"birthDate" timestamp(3)   ,"createdAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"email" text  NOT NULL ,"id" SERIAL,"name" text   ,"updatedAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"username" text  NOT NULL ,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."Location" (
"city" text  NOT NULL ,"createdAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"id" SERIAL,"lat" Decimal(65,30)  NOT NULL ,"long" Decimal(65,30)  NOT NULL ,"updatedAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"userId" integer  NOT NULL ,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."Learning" (
"authorId" integer  NOT NULL ,"createdAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"id" SERIAL,"type" "LearningType" NOT NULL DEFAULT E'SUCCESS',"updatedAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."Tag" (
"createdAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"id" SERIAL,"name" text  NOT NULL ,"updatedAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."Like" (
"createdAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"id" SERIAL,"learningId" integer  NOT NULL ,"updatedAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"userId" integer  NOT NULL ,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."Comment" (
"body" text  NOT NULL ,"createdAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"id" SERIAL,"learningId" integer  NOT NULL ,"updatedAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"userId" integer  NOT NULL ,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."_LearningToTag" (
"A" integer  NOT NULL ,"B" integer  NOT NULL )

CREATE UNIQUE INDEX "User.email" ON "public"."User"("email")

CREATE UNIQUE INDEX "User.username" ON "public"."User"("username")

CREATE UNIQUE INDEX "Location_userId" ON "public"."Location"("userId")

CREATE UNIQUE INDEX "_LearningToTag_AB_unique" ON "public"."_LearningToTag"("A","B")

CREATE  INDEX "_LearningToTag_B_index" ON "public"."_LearningToTag"("B")

ALTER TABLE "public"."Location" ADD FOREIGN KEY ("userId")REFERENCES "public"."User"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."Learning" ADD FOREIGN KEY ("authorId")REFERENCES "public"."User"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."Like" ADD FOREIGN KEY ("userId")REFERENCES "public"."User"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."Like" ADD FOREIGN KEY ("learningId")REFERENCES "public"."Learning"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."Comment" ADD FOREIGN KEY ("userId")REFERENCES "public"."User"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."Comment" ADD FOREIGN KEY ("learningId")REFERENCES "public"."Learning"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."_LearningToTag" ADD FOREIGN KEY ("A")REFERENCES "public"."Learning"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."_LearningToTag" ADD FOREIGN KEY ("B")REFERENCES "public"."Tag"("id") ON DELETE CASCADE  ON UPDATE CASCADE
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration ..20200815133553-migration
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,77 @@
+datasource DS {
+  provider = "postgresql"
+  url = "***"
+}
+
+generator client {
+  provider      = "prisma-client-js"
+  binaryTargets = env("BINARY_TARGET")
+}
+
+model User {
+  id        Int       @id @default(autoincrement())
+  email     String    @unique
+  name      String?
+  location  Location?
+  birthDate DateTime?
+  username  String    @unique
+  createdAt DateTime  @default(now())
+  updatedAt DateTime  @default(now())
+}
+
+model Location {
+  id        Int      @id @default(autoincrement())
+  city      String
+  lat       Float
+  long      Float
+  userId    Int
+  user      User     @relation(fields: [userId], references: [id])
+  createdAt DateTime @default(now())
+  updatedAt DateTime @default(now())
+}
+
+model Learning {
+  id        Int          @id @default(autoincrement())
+  author    User         @relation(fields: [authorId], references: [id])
+  authorId  Int
+  tags      Tag[]        @relation(references: [id])
+  likes     Like[]
+  comments  Comment[]
+  type      LearningType @default(SUCCESS)
+  createdAt DateTime     @default(now())
+  updatedAt DateTime     @default(now())
+}
+
+model Tag {
+  id        Int        @id @default(autoincrement())
+  name      String
+  learnings Learning[] @relation(references: [id])
+  createdAt DateTime   @default(now())
+  updatedAt DateTime   @default(now())
+}
+
+model Like {
+  id         Int      @id @default(autoincrement())
+  userId     Int
+  user       User     @relation(fields: [userId], references: [id])
+  learningId Int
+  learning   Learning @relation(fields: [learningId], references: [id])
+  createdAt  DateTime @default(now())
+  updatedAt  DateTime @default(now())
+}
+
+model Comment {
+  id         Int      @id @default(autoincrement())
+  userId     Int
+  user       User     @relation(fields: [userId], references: [id])
+  body       String
+  learningId Int
+  learning   Learning @relation(fields: [learningId], references: [id])
+  createdAt  DateTime @default(now())
+  updatedAt  DateTime @default(now())
+}
+
+enum LearningType {
+  SUCCESS
+  FAILURE
+}
```


