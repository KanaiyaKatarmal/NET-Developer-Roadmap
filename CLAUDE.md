# CLAUDE.md

## Project Overview

This project is a .NET 10 REST API built using Clean Architecture and
Vertical Slice Architecture. It follows CQRS principles and uses Minimal
APIs, FluentValidation, and Entity Framework Core with PostgreSQL.

Claude should follow the architectural rules defined in this document
when generating or modifying code.

------------------------------------------------------------------------

## Tech Stack

Runtime - .NET 10 - ASP.NET Core Minimal APIs

Database - PostgreSQL - Entity Framework Core - Npgsql

Validation - FluentValidation

Architecture - Clean Architecture - Vertical Slice Architecture - CQRS

Tools - Scalar OpenAPI UI - Health Checks - Logging Decorators - EF Core
Audit Interceptor

------------------------------------------------------------------------

## Architecture Layers

WebApi → Infrastructure → Application → Domain

Rules:

Domain - Contains entities and business rules - Must not depend on any
other layer

Application - Contains features (vertical slices) - Depends only on
Domain

Infrastructure - Implements persistence and external services - Depends
on Application and Domain

WebApi - Application entry point - Composes dependencies and middleware

------------------------------------------------------------------------

## Vertical Slice Architecture

Each feature must be self-contained.

Example structure:

Application/Features/Book/CreateBook/

-   CreateBookHandler.cs
-   CreateBookValidator.cs
-   CreateBookEndpoint.cs

Rules: - Each feature owns its handler, validator, and endpoint -
Business logic must be inside handlers - Endpoints only handle HTTP
mapping

------------------------------------------------------------------------

## CQRS Pattern

Commands modify state: - Create - Update - Delete

Queries read state: - GetAll - GetById

Handlers implement:

IHandler\<TRequest, TResponse\>

Handlers return:

Result`<T>`{=html}

------------------------------------------------------------------------

## DTO Rules

All request and response models must use C# records.

Example:

public sealed record CreateBookRequest( string Title, string Author,
string ISBN, decimal Price, int PublishedYear);

------------------------------------------------------------------------

## Validation

All requests must be validated using FluentValidation.

Validators are placed inside the feature folder.

Example:

RuleFor(x =\> x.Title) .NotEmpty() .MaximumLength(200);

------------------------------------------------------------------------

## Repository Usage

Application layer uses abstractions:

-   IRepository`<T>`{=html}
-   IUnitOfWork

Infrastructure layer implements them.

Handlers must never access DbContext directly.

------------------------------------------------------------------------

## Entity Rules

All entities must inherit from:

AuditableEntity

Fields:

-   CreatedOn
-   UpdatedOn

These are automatically set using AuditInterceptor.

------------------------------------------------------------------------

## Error Handling

Handlers return Result`<T>`{=html}.

Success:

return Result.Success(response);

Failure:

return Result.Failure`<ResponseType>`{=html}(Error);

------------------------------------------------------------------------

## API Rules

Endpoints must:

-   Use Minimal APIs
-   Be thin
-   Delegate logic to handlers
-   Return correct HTTP status codes

------------------------------------------------------------------------

## Database

PostgreSQL connection string is defined in:

appsettings.json

Migration commands:

Create migration:

dotnet ef migrations add MigrationName -p src/Infrastructure -s
src/WebApi

Update database:

dotnet ef database update -p src/Infrastructure -s src/WebApi

------------------------------------------------------------------------

## Health Checks

Health endpoint:

/health

Used to monitor database connectivity and service status.

------------------------------------------------------------------------

## API Documentation

OpenAPI spec:

/openapi/v1.json

Scalar UI:

/scalar/v1

------------------------------------------------------------------------

## Adding a New Feature

1.  Create folder:

Application/Features/NewFeature

2.  Add:

-   Handler
-   Validator
-   Endpoint

3.  Define request and response records

4.  Implement business logic in the handler

Auto-discovery registers handlers, validators, and endpoints
automatically.

------------------------------------------------------------------------

## Code Generation Rules

Claude must:

-   Follow Clean Architecture boundaries
-   Use Vertical Slice pattern
-   Use CQRS
-   Use FluentValidation
-   Use records for DTOs
-   Use async database calls
-   Return Result`<T>`{=html}

------------------------------------------------------------------------

## Files Claude Must Not Modify

-   bin/
-   obj/
-   Migrations/
-   \*.csproj
-   appsettings.Production.json

Only modify source code inside:

-   Domain
-   Application
-   Infrastructure
-   WebApi

------------------------------------------------------------------------

## Workflow

Before making changes Claude must:

1.  Analyze the feature
2.  Propose a plan
3.  Identify files to change
4.  Implement solution
5.  Ensure architecture rules are respected

------------------------------------------------------------------------

Goal: Maintain a clean, scalable .NET API using Clean Architecture,
Vertical Slice Architecture, and CQRS.
