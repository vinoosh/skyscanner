# skyscanner
package com.skyscanner;

import com.fasterxml.jackson.annotation.JsonProperty;

public class Search {

    @JsonProperty
    private String city;

    public Search() {
    }

    public Search(String city) {
        this.city = city;
    }

    public String getCity() {
        return city;
    }
}



package com.skyscanner;

import com.fasterxml.jackson.annotation.JsonProperty;

public class SearchResult {

    @JsonProperty
    private String city;

    @JsonProperty
    private String title;

    @JsonProperty
    private String kind;

    public SearchResult() {
    }

    public SearchResult(String city, String title, String kind) {
        this.city = city;
        this.title = title;
        this.kind = kind;
    }

    public String getCity() {
        return city;
    }

    public String getTitle() {
        return title;
    }

    public String getKind() {
        return kind;
    }
}


@Override
public void run(final HoenScannerConfiguration configuration, final Environment environment) throws IOException {
    ObjectMapper mapper = new ObjectMapper();

    List<SearchResult> carResults = Arrays.asList(
            mapper.readValue(
                    getClass().getClassLoader().getResource("rental_cars.json"),
                    SearchResult[].class
            )
    );

    List<SearchResult> hotelResults = Arrays.asList(
            mapper.readValue(
                    getClass().getClassLoader().getResource("hotels.json"),
                    SearchResult[].class
            )
    );

    List<SearchResult> searchResults = new ArrayList<>();
    searchResults.addAll(carResults);
    searchResults.addAll(hotelResults);

    final SearchResource resource = new SearchResource(searchResults);
    environment.jersey().register(resource);
}


package com.skyscanner;

import jakarta.validation.Valid;
import jakarta.validation.constraints.NotNull;
import jakarta.ws.rs.Consumes;
import jakarta.ws.rs.POST;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;
import java.util.ArrayList;
import java.util.List;

@Path("/search")
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
public class SearchResource {

    private List<SearchResult> searchResults;

    public SearchResource(List<SearchResult> searchResults) {
        this.searchResults = searchResults;
    }

    @POST
    public List<SearchResult> search(@NotNull @Valid Search search) {
        List<SearchResult> response = new ArrayList<>();
        for (SearchResult result : searchResults) {
            if (result.getCity().equals(search.getCity())) {
                response.add(result);
            }
        }
        return response;
    }
}


