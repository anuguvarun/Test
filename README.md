String formattedDate =
            LocalDateTime.now(ZoneOffset.UTC)
                    .format(RepositoryHelperConstants.dateTimeFormatter);

    response.setCurrentSystemDateTime(formattedDate);

    return response;
}