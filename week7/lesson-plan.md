# Week 7 : Connecting frontend and backend

## Pre-requisites:

Having a finished week 6 homework and **Preparation**.

## Agenda

1. Q&A - 30m
2. Go over the setup from the preparation

## Task

Continue working on mealsharing project from the preparation. As a first step add package reference to the `Dapper` and `MySql.Data`.
1. Meals
  * Create an endpoint for fetching/searching the meals - endpoint route should be based on the needs of your frontend. In most cases it's `GET /api/meals` (if you followed preparation you already have this endpoint that is returning dummy data).
  * Create `IMealRepository` interface with `Search()` method and implement it as a class `MealRepository`.
  * Call `IMealRepository.Search` from the endpoint and test it from the UI. Make sure that shape of the data is the same as in nodejs project (eg number of fields and field names).
  * Try extending Search with `string filterCriteria`
2. Implement reservations
  * Create an endpoint for fetching/searching the reservations - endpoint route should be based on the needs of your frontend. In most cases it's `GET /api/reservations`.
  * Create `IReservationsRepository` interface with `GetAll()` method and implement it as a class `ReservationsRepository`.
  * Call `IReservationsRepository.GetAll` from the endpoint and test it from the UI.
3. Implement reviews
  * Create an endpoint for fetching/searching the reviews - endpoint route should be based on the needs of your frontend. In most cases it's `GET /api/reviews`.
  * Create `IReviewsRepository` interface with `GetAll()` method and implement it as a class `ReviewsRepository`.
  * Call `IReviewsRepository.GetAll` from the endpoint and test it from the UI.
