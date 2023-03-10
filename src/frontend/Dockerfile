# Use base golang image from Docker Hub
FROM golang:1.19 as build

WORKDIR /app

# Copy the go.mod and go.sum, download the dependencies
COPY go.mod go.sum ./
RUN go mod download

# Copy rest of the application source code
COPY . ./

# Compile the application to /app/frontend.
# Skaffold passes in debug-oriented compiler flags
ARG SKAFFOLD_GO_GCFLAGS
RUN echo "Go gcflags: ${SKAFFOLD_GO_GCFLAGS}"
RUN go build -gcflags="${SKAFFOLD_GO_GCFLAGS}" -mod=readonly -v -o /app/frontend .

# Now create separate deployment image
FROM gcr.io/distroless/base

# Definition of this variable is used by 'skaffold debug' to identify a golang binary.
# Default behavior - a failure prints a stack trace for the current goroutine.
# See https://golang.org/pkg/runtime/
ENV GOTRACEBACK=single

WORKDIR /app
COPY static /app/static
COPY templates /app/templates
COPY --from=build /app/frontend /app/frontend
ENTRYPOINT ["/app/frontend"]
