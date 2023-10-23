# Exception Handing in Spring with Controller Advice

A best practice pattern for error handling in Spring applications is to use a `@ControllerAdvice` the centralize and add consistency to error and exception handling.

## Inplementation

Existing exceptions can be handled via handler methods in the `@ControllerAdvice`.  Custom exceptions can also be created and handled if applicable.

```java

public class PatientEventException extends RuntimeException {

    private String message;

    public PatientEventException() {
        super("A patient event error has occurred!");
    }

    public PatientEventException(String message) {
        super(message);
    }
}
```

Create a error handler class that will control the behavior of the application when prescribed errors and exceptions are thrown.

```java
import com.cvshealth.patient.event.PatientEventException;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.context.request.WebRequest;
import org.springframework.web.servlet.mvc.method.annotation.ResponseEntityExceptionHandler;

@Slf4j
@ControllerAdvice
public class PatientExceptionHandler extends ResponseEntityExceptionHandler {

    @ExceptionHandler( value = { PatientEventException.class })
    public ResponseEntity<Object> handlePatientEventException(PatientEventException exception, WebRequest request) {
        String responseBody = "An error occurred while creating a patient";
        log.debug(responseBody);
        return handleExceptionInternal(exception, responseBody, new HttpHeaders(), HttpStatus.INTERNAL_SERVER_ERROR, request);
    }

}
```

Here is a snippet from a REST controller where an exception is thrown if the new patient creation fails.

```java

    @PostMapping(consumes = APPLICATION_JSON_VALUE)
    @Operation(summary = "Creates a patient", method = "POST", tags = "Patient CRUD")
    @ApiResponse(
            responseCode = "201",
            description = "Patient successfully created",
            headers = @Header(
                    name = "Location",
                    description = "Contains path which can be used to retrieve saved patient",
                    required = true,
                    schema = @Schema(type = "string"))
    )
    @ApiResponse(
            responseCode = "400",
            description = "Passed patient data is invalid"
    )
    public ResponseEntity<Void> createPatient(@RequestBody @Valid PatientModificationRequest request) {
        NewPatient newPatient = mapper.toNewPatient(request);
        Patient savedPatient = patientService.create(newPatient);
        if(null == savedPatient) {
            throw new PatientEventException();
        }
        return created(toLocationUri(savedPatient.getId())).build();
    }

```

### Testing

Handling exceptions via a controller advice can be tested just like any other exception handing pattern.

```java
import com.cvshealth.patient.domain.NewPatient;
import com.cvshealth.patient.domain.PersistedKafkaPatientService;
import com.cvshealth.patient.event.PatientEventException;
import jakarta.validation.Valid;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.when;

class PatientExceptionHandlerTest {

    private static final String APPLICATION_PORT = "8080";
    private static final String BASE_URL = "http://localhost:" + APPLICATION_PORT;
    private static final String EXCEPTION_ENDPOINT = BASE_URL + "/exception";

    PatientController controller;

    PersistedKafkaPatientService mockPatientService;

    ApiMapper mockApiMapper;

    @BeforeEach
    void setUp() {
        mockPatientService = mock(PersistedKafkaPatientService.class);
        mockApiMapper = mock(ApiMapper.class);
        controller = new PatientController(mockPatientService, mockApiMapper);
    }

    @Test
    void whenPatientCreationFails_thenReturnInternalServerError() {
        // Given
        @Valid PatientModificationRequest patientModificationRequest = new PatientModificationRequest("first-name", "last-name", "some@mail.com");
        when(mockPatientService.create(any(NewPatient.class))).thenReturn(null);
        // When
        assertThrows(PatientEventException.class, () -> {
            ResponseEntity<Void> response = controller.createPatient(patientModificationRequest);
        });
        // Then
    }
}
```


## References

- [Error Handling for REST with Spring](https://www.baeldung.com/exception-handling-for-rest-with-spring)
- [Exception Handling in Spring MVC](https://spring.io/blog/2013/11/01/exception-handling-in-spring-mvc)