@Query(value = """
    SELECT COUNT(DISTINCT tor.tradeRequestId)
    FROM TradeOrderRequests tor
    JOIN TradeOrderRequestItems tori
         ON tor.tradeRequestId = tori.tradeRequestId
    WHERE tor.reportStatus IS NULL
      AND tori.tradeStatusCode IN (:itemStatuses)
      AND tor.createdDate >= :fromDate
""")
Long getUnApprovedCount(
        @Param("itemStatuses") List<String> itemStatuses,
        @Param("fromDate") LocalDateTime fromDate
);