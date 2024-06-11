---
title: EPICS Archiver Appliance - Matlab Integration
viewport: width=device-width, initial-scale=1
---

::::: container-fluid
::: navbar-header
[The EPICS Archiver Appliance](index.html){.navbar-brand}
:::

<div>

-   [News](https://github.com/slacmshankar/epicsarchiverap/wiki)
-   [Details](details.html)
-   [Download](https://github.com/slacmshankar/epicsarchiverap/releases/)
-   [Quickstart](quickstart.html)
-   [Installation](installguide.html)
-   [User Guide](userguide.html)
-   [Customize](customization.html)
-   [Developers](developersguide.html)
-   [Admin](admin.html)
-   [Javadoc](api/index.html)
-   [Issues](https://github.com/slacmshankar/epicsarchiverap/issues)
-   [FAQ](faq.html)
-   [License](license.html)

<!-- -->

-   ![](images/Icon_Mathematical_Plot.png){style="margin-top: -45px"}

</div>
:::::

::::: container
::: section
### Retrieving data using Matlab

The EPICS Archiver Appliance support multiple MIME-types when retrieving
data and one of the supported MIME-types is the Matlab file format. The
process of getting archive data into Matlab consists of

1.  Constructing a URL to the server. See
    [this](userguide.html#retrieval_url_formula) section on for the URL
    format for data retrieval.
2.  Using `urlwrite` to download data from this URL into a file.
3.  Loading this file into Matlab.

Here\'s some sample code that is used to get the data for PV
`VPIO:IN20:111:VRAW`


    urlwrite('http://test-arch.slac.stanford.edu:17668/retrieval/data/getData.mat', ...
        'temp.mat', 'get', ...
        {'pv', 'VPIO:IN20:111:VRAW', 'from', '2012-09-27T08:00:00.000Z', 'to', '2012-09-28T08:00:00.000Z'})

    dat = load('temp.mat')
    delete('temp.mat')

    try
        header = dat.header;
        data = dat.data;
    catch
        fprintf('Could not get data from file\n');
        return
    end

    header
    data

Here\'s a screenshot of this sample code being executed in a Matlab
environment.

![image](images/matlab1.png){.img-responsive}
:::

<div>

The `.mat` response contains two objects, a header and a data object.

-   The header object is indexed by the string `header` and is a
    MLStructure with fields containing some provenance information.
-   The data object is indexed by the string `data` and is a MLStructure
    with these fields.
    1.  `epochSeconds` - contains Java epoch seconds as a 1x1 uint64
        array. The times are in UTC; so any conversion to local time
        needs to happen at the client.
    2.  `values` - contains the values for the samples. All scalars come
        as a 1x1 double array. Waveforms come as a 1x*elementcount*
        double array where *elementcount* is the EPICS element count of
        the waveform.
    3.  `nanos` - contains the nano second value of the EPICS record
        processing timestamp as a 1x1 uint64 array. Some installations
        embed the beam code/pulse id into this field.
    4.  `isDST` - contains booleans that indicate if the time indicated
        by `epochSeconds` was in daylight savings time in the timezone
        of the server.

</div>
:::::
