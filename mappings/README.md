# RML Mapping for HSE Ontology

This directory contains RML (RDF Mapping Language) configuration and CSV source files for generating the HSE Population Health Ontology in RDF/Turtle format.

## Overview

The mapping process converts three CSV files containing ontology definitions into a single Turtle (.ttl) file that represents the complete ontology with:

- Class definitions (rdfs:Class)
- Datatype properties (owl:DatatypeProperty)
- Object properties (owl:ObjectProperty)
- Ontology metadata for phi: and phd: namespaces

## Prerequisites

Install RMLMapper:

```bash
# download from: https://github.com/RMLio/rmlmapper-java/releases
```

## File Structure

```
mappings/
├── config.ttl                    # RML mapping configuration
├── classes.csv                   # Class definitions
├── datatype_properties.csv       # Datatype property definitions
├── object_properties.csv         # Object property definitions
├── output.ttl                    # Generated ontology (output)
└── README.md                     # This file
```

## CSV Files Format

### classes.csv

Columns: `URI`, `Label`, `Comment`, `SubClassOf`, `Source`, `Creator`, `Contributor`, `Created`, `Modified`, `SeeAlso`

### datatype_properties.csv

Columns: `URI`, `Label`, `Comment`, `SubPropertyOf`, `Domain`, `Range`, `Source`, `Creator`, `Contributor`, `Created`, `Modified`, `SeeAlso`

### object_properties.csv

Columns: `URI`, `Label`, `Comment`, `SubPropertyOf`, `Domain`, `Range`, `Source`, `Creator`, `Contributor`, `Created`, `Modified`, `SeeAlso`

**Important:** All URIs in the CSV files must be fully qualified (e.g., `http://www.w3.org/ns/dcat#Dataset`), not prefixed (e.g., ~~`dcat:Dataset`~~).

## Running the Mapping

From the project root:

```bash
rmlmapper -m ./mappings/config.ttl -o ./mappings/output.ttl -s turtle
```

Or from within the mappings directory:

```bash
cd mappings
rmlmapper -m config.ttl -o output.ttl -s turtle
```

### Command Options

- `-m` : Path to the RML mapping configuration file
- `-o` : Path to the output file
- `-s` : Output serialization format (`turtle`, `ntriples`, `rdfxml`, etc.)

## Output

The generated `output.ttl` file contains:

- All classes from `classes.csv` as `rdfs:Class` instances
- All datatype properties from `datatype_properties.csv` as `owl:DatatypeProperty` instances
- All object properties from `object_properties.csv` as `owl:ObjectProperty` instances
- Static ontology metadata for `phi:` (Population Health Indicator Ontology)
- Static ontology metadata for `phd:` (Population Health Datasets Ontology)

The output uses standard prefixes:

```turtle
@prefix phi: <https://hse.ie/ontology/phi#> .
@prefix phd: <https://hse.ie/ontology/phd#> .
@prefix dcterms: <http://purl.org/dc/terms/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix owl: <http://www.w3.org/2002/07/owl#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix dcat: <http://www.w3.org/ns/dcat#> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
```

## Troubleshooting

### Error: "Not a valid (absolute) IRI"

**Cause:** Missing `@base` directive in config.ttl  
**Solution:** Ensure the first line of config.ttl is `@base <http://hse.ie/mappings/> .`

### Error: URL-encoded URIs in output (e.g., `%3A%2F%2F`)

**Cause:** Using `rr:template "{URI}"` instead of `rml:reference "URI"`  
**Solution:** Subject maps should use:

```turtle
rr:subjectMap [
  rml:reference "URI" ;
  rr:termType rr:IRI ;
  rr:class owl:DatatypeProperty
] ;
```

### Prefixed names not expanding (e.g., `<xsd:string>`)

**Cause:** CSV files contain prefixed names instead of full URIs  
**Solution:** Replace all prefixed names in CSV files with full URIs:

- `xsd:string` → `http://www.w3.org/2001/XMLSchema#string`
- `dcat:Dataset` → `http://www.w3.org/ns/dcat#Dataset`
- `dcterms:title` → `http://purl.org/dc/terms/title`

### Empty or missing ontology metadata

**Cause:** Ontology metadata mappings missing logical source  
**Solution:** Ensure ontology metadata mappings include:

```turtle
rml:logicalSource [
  rml:source "classes.csv" ;
  rml:referenceFormulation ql:CSV ;
  rml:iterator "$"
] ;
```

## Workflow

1. **Edit CSV files** with your ontology definitions
2. **Ensure all URIs are fully qualified** (not prefixed)
3. **Run the mapping** using rmlmapper
4. **Validate output** using an RDF validator or load into Protégé
5. **Commit changes** to version control

## Updating the Ontology

When making changes to the ontology:

1. Edit the appropriate CSV file(s)
2. Update the `dcterms:modified` date in config.ttl for ontology metadata
3. Increment `owl:versionInfo` if needed
4. Run the mapping to regenerate output.ttl
5. Review the changes in output.ttl

## References

- [RML Specification](https://rml.io/specs/rml/)
- [RMLMapper Documentation](https://github.com/RMLio/rmlmapper-java)
- [R2RML Specification](https://www.w3.org/TR/r2rml/)
- [DCAT Vocabulary](https://www.w3.org/TR/vocab-dcat-3/)
- [Dublin Core Terms](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/)

## Version History

- **v1.0.1** (2026-02-03) - Added datatype and object property mappings
- **v1.0.0** (2025-08-20) - Initial version with class mappings
